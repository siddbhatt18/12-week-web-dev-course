### Week 10 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 10:**  
Turn what you learned with Notes + Auth + React into a more “real” **Task Manager**:

- Add a **Task** model and API (backend)
- Build a **React Task Dashboard** (frontend)
- Implement status, filtering, editing, and better UX
- Clean up code with simple patterns (custom hooks, reusable UI)

I’ll assume:

- You have the Week 8/9 project running:
  - Backend: Express + Mongo + JWT auth + protected `/api/notes`
  - Frontend: React app with:
    - `AuthContext`, `api.js`
    - `Login` / `Register` / `Dashboard` (notes) pages

You’ll now **add Tasks alongside Notes**, then gradually focus your React UI on Tasks. You can keep Notes for reference.

---

## Day 1 – Design & Build the Task API (Backend)

### Goals
- Design a Task data model.
- Implement **CRUD** routes for tasks on the backend.
- Protect tasks with auth and tie them to a user.

### Concepts
- Data modeling: deciding fields for a Task.
- Reusing patterns from Notes (80–20).
- Route structure: `/api/tasks` similar to `/api/notes`.

### Step‑by‑Step Tasks

1. **Design your Task model (think first, then code)**  
   In `notes-week10-day1.md` sketch:

   - Fields you want for a Task, e.g.:

     ```text
     title:        String, required, short label
     description:  String, optional/longer text
     status:       String, one of ["todo", "in-progress", "done"]
     dueDate:      Date, optional
     user:         ObjectId -> User (owner)
     ```

   - Which fields are required? Reason briefly.

2. **Create `models/Task.js`**
   - In `models/Task.js`:

     ```js
     const mongoose = require("mongoose");

     const taskSchema = new mongoose.Schema(
       {
         title: {
           type: String,
           required: [true, "Title is required"],
           trim: true,
           minlength: [3, "Title must be at least 3 characters"],
         },
         description: {
           type: String,
           trim: true,
           default: "",
         },
         status: {
           type: String,
           enum: ["todo", "in-progress", "done"],
           default: "todo",
         },
         dueDate: {
           type: Date,
         },
         user: {
           type: mongoose.Schema.Types.ObjectId,
           ref: "User",
           required: true,
         },
       },
       { timestamps: true }
     );

     const Task = mongoose.model("Task", taskSchema);

     module.exports = Task;
     ```

3. **Create `routes/taskRoutes.js`**
   - In `routes/taskRoutes.js`:

     ```js
     const express = require("express");
     const Task = require("../models/Task");
     const authMiddleware = require("../middleware/auth");

     const router = express.Router();

     // All task routes are protected
     router.use(authMiddleware);

     // GET /api/tasks
     router.get("/", async (req, res, next) => {
       try {
         const tasks = await Task.find({ user: req.user._id }).sort({
           createdAt: -1,
         });
         res.json(tasks);
       } catch (error) {
         next(error);
       }
     });

     // POST /api/tasks
     router.post("/", async (req, res, next) => {
       try {
         const { title, description, status, dueDate } = req.body;
         if (!title) {
           return res.status(400).json({ error: "Title is required" });
         }

         const task = await Task.create({
           title,
           description,
           status,
           dueDate,
           user: req.user._id,
         });

         res.status(201).json(task);
       } catch (error) {
         next(error);
       }
     });

     // GET /api/tasks/:id
     router.get("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const task = await Task.findOne({ _id: id, user: req.user._id });
         if (!task) {
           return res.status(404).json({ error: "Task not found" });
         }
         res.json(task);
       } catch (error) {
         next(error);
       }
     });

     // PATCH /api/tasks/:id
     router.patch("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const updates = req.body;

         const task = await Task.findOneAndUpdate(
           { _id: id, user: req.user._id },
           updates,
           { new: true, runValidators: true }
         );

         if (!task) {
           return res.status(404).json({ error: "Task not found" });
         }

         res.json(task);
       } catch (error) {
         next(error);
       }
     });

     // DELETE /api/tasks/:id
     router.delete("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const deleted = await Task.findOneAndDelete({
           _id: id,
           user: req.user._id,
         });

         if (!deleted) {
           return res.status(404).json({ error: "Task not found" });
         }

         res.status(204).send();
       } catch (error) {
         next(error);
       }
     });

     module.exports = router;
     ```

4. **Wire routes in `server.js`**
   - In `server.js`:

     ```js
     const taskRouter = require("./routes/taskRoutes");
     app.use("/api/tasks", taskRouter);
     ```

5. **Test with Postman**
   - Login, copy token.
   - Set Authorization: Bearer token.
   - Test:
     - `POST /api/tasks`
     - `GET /api/tasks`
     - `PATCH /api/tasks/:id`
     - `DELETE /api/tasks/:id`

### Practice Exercises
- Add a `priority` field (`"low" | "medium" | "high"`) to the Task schema and send it in POST requests.
- Try creating tasks for two different users (two accounts). Confirm each only sees their own tasks.

### Reflection
In `notes-week10-day1.md`:

- Why did you include `user` in the Task schema?
- When updating or deleting, why do you always filter by `{ _id: id, user: req.user._id }` instead of just `_id`?

---

## Day 2 – Task Filtering, Sorting & Stats (Backend)

### Goals
- Add **status filtering** (`todo`, `in-progress`, `done`).
- Add basic **sorting** and an optional **stats endpoint**.
- Think about what should be computed on the server vs the client.

### Concepts
- Query parameters: `?status=...&sort=...`
- Server‑side aggregation vs client‑side computation (80–20: start simple).

### Step‑by‑Step Tasks

1. **Extend GET /api/tasks with query filters**
   - In `taskRoutes.js`, update GET `/`:

     ```js
     router.get("/", async (req, res, next) => {
       try {
         const { status, sort } = req.query;

         const query = { user: req.user._id };
         if (status && ["todo", "in-progress", "done"].includes(status)) {
           query.status = status;
         }

         let sortOption = { createdAt: -1 }; // default: newest first
         if (sort === "oldest") {
           sortOption = { createdAt: 1 };
         } else if (sort === "dueDate") {
           sortOption = { dueDate: 1, createdAt: -1 };
         }

         const tasks = await Task.find(query).sort(sortOption);
         res.json(tasks);
       } catch (error) {
         next(error);
       }
     });
     ```

2. **Add a simple stats endpoint**
   - In `taskRoutes.js`:

     ```js
     router.get("/stats/summary", async (req, res, next) => {
       try {
         const userId = req.user._id;
         const total = await Task.countDocuments({ user: userId });
         const todo = await Task.countDocuments({ user: userId, status: "todo" });
         const inProgress = await Task.countDocuments({
           user: userId,
           status: "in-progress",
         });
         const done = await Task.countDocuments({ user: userId, status: "done" });

         res.json({ total, todo, inProgress, done });
       } catch (error) {
         next(error);
       }
     });
     ```

3. **Test with Postman**
   - With your token:
     - `GET /api/tasks`
     - `GET /api/tasks?status=todo`
     - `GET /api/tasks?sort=dueDate`
     - `GET /api/tasks/stats/summary`

### Practice Exercises
- Add an optional `?search=` query that searches title/description similar to how you did for notes.
- Try combining `status` + `sort` + `search` in one request.

### Reflection
In `notes-week10-day2.md`:

- Which filters/sorts do you prefer to compute on the server? Which are easy to handle on the client only?
- How could the stats endpoint be useful in your React dashboard?

---

## Day 3 – React: Task API Layer & Basic Task Dashboard

### Goals
- Add **task API functions** to your React `api.js`.
- Build a new React **TaskDashboard** page.
- List tasks and create new tasks from the React UI.

### Concepts
- Keeping API calls in one “API client” file.
- Reusing patterns from your notes dashboard.
- Integrating with auth context (token + `Authorization` header).

### Step‑by‑Step Tasks

1. **Extend React `api.js` with task calls**
   - In `week9-react-client/src/api.js` (or your equivalent):

     ```js
     export async function fetchTasksApi({ status, sort } = {}) {
       const params = new URLSearchParams();
       if (status) params.set("status", status);
       if (sort) params.set("sort", sort);

       const url = params.toString()
         ? `${API_BASE}/api/tasks?${params.toString()}`
         : `${API_BASE}/api/tasks`;

       const res = await fetch(url, {
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }

     export async function createTaskApi({ title, description, status, dueDate }) {
       const res = await fetch(`${API_BASE}/api/tasks`, {
         method: "POST",
         headers: getAuthHeaders(),
         body: JSON.stringify({ title, description, status, dueDate }),
       });
       return handleResponse(res);
     }

     export async function deleteTaskApi(id) {
       const res = await fetch(`${API_BASE}/api/tasks/${id}`, {
         method: "DELETE",
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }

     export async function updateTaskApi(id, updates) {
       const res = await fetch(`${API_BASE}/api/tasks/${id}`, {
         method: "PATCH",
         headers: getAuthHeaders(),
         body: JSON.stringify(updates),
       });
       return handleResponse(res);
     }

     export async function fetchTaskStatsApi() {
       const res = await fetch(`${API_BASE}/api/tasks/stats/summary`, {
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }
     ```

2. **Create `TaskDashboardPage.jsx`**
   - In `src/TaskDashboardPage.jsx`:

     ```jsx
     import { useEffect, useState } from "react";
     import { useAuth } from "./AuthContext.jsx";
     import {
       fetchTasksApi,
       createTaskApi,
       deleteTaskApi,
       fetchTaskStatsApi,
     } from "./api.js";

     function TaskDashboardPage() {
       const { user, logout } = useAuth();
       const [tasks, setTasks] = useState([]);
       const [loading, setLoading] = useState(false);
       const [error, setError] = useState("");
       const [title, setTitle] = useState("");
       const [description, setDescription] = useState("");
       const [statusFilter, setStatusFilter] = useState("");
       const [sort, setSort] = useState("newest");
       const [stats, setStats] = useState(null);

       async function loadTasks(currentStatus = statusFilter, currentSort = sort) {
         setLoading(true);
         setError("");
         try {
           const data = await fetchTasksApi({
             status: currentStatus || undefined,
             sort: currentSort === "newest" ? undefined : currentSort,
           });
           setTasks(data);
           const s = await fetchTaskStatsApi();
           setStats(s);
         } catch (err) {
           setError(err.message);
         } finally {
           setLoading(false);
         }
       }

       useEffect(() => {
         loadTasks();
       }, []);

       async function handleAddTask(e) {
         e.preventDefault();
         const trimmedTitle = title.trim();
         const trimmedDescription = description.trim();
         if (!trimmedTitle) return;
         try {
           await createTaskApi({
             title: trimmedTitle,
             description: trimmedDescription,
           });
           setTitle("");
           setDescription("");
           loadTasks();
         } catch (err) {
           setError(err.message);
         }
       }

       async function handleDeleteTask(id) {
         if (!confirm("Delete this task?")) return;
         try {
           await deleteTaskApi(id);
           loadTasks();
         } catch (err) {
           setError(err.message);
         }
       }

       return (
         <div>
           <header style={{ display: "flex", justifyContent: "space-between" }}>
             <div>
               <h2>Task Dashboard</h2>
               {user && <p>Logged in as {user.email}</p>}
             </div>
             <button onClick={logout}>Logout</button>
           </header>

           {stats && (
             <div>
               <p>
                 Total: {stats.total} | Todo: {stats.todo} | In-Progress:{" "}
                 {stats.inProgress} | Done: {stats.done}
               </p>
             </div>
           )}

           {error && <p style={{ color: "red" }}>Error: {error}</p>}
           {loading && <p>Loading tasks...</p>}

           <section>
             <h3>Create Task</h3>
             <form onSubmit={handleAddTask}>
               <input
                 type="text"
                 placeholder="Title"
                 value={title}
                 onChange={(e) => setTitle(e.target.value)}
               />
               <textarea
                 placeholder="Description"
                 value={description}
                 onChange={(e) => setDescription(e.target.value)}
               />
               <button type="submit">Add Task</button>
             </form>
           </section>

           <section>
             <h3>Tasks</h3>
             <div>
               <label>
                 Status:
                 <select
                   value={statusFilter}
                   onChange={(e) => {
                     const value = e.target.value;
                     setStatusFilter(value);
                     loadTasks(value, sort);
                   }}
                 >
                   <option value="">All</option>
                   <option value="todo">Todo</option>
                   <option value="in-progress">In-Progress</option>
                   <option value="done">Done</option>
                 </select>
               </label>

               <label style={{ marginLeft: "8px" }}>
                 Sort:
                 <select
                   value={sort}
                   onChange={(e) => {
                     const value = e.target.value;
                     setSort(value);
                     loadTasks(statusFilter, value);
                   }}
                 >
                   <option value="newest">Newest first</option>
                   <option value="oldest">Oldest first</option>
                   <option value="dueDate">By due date</option>
                 </select>
               </label>
             </div>

             <ul>
               {tasks.map((task) => (
                 <li key={task._id}>
                   <strong>{task.title}</strong>{" "}
                   <span>({task.status || "todo"})</span>
                   {task.description && <p>{task.description}</p>}
                   <button onClick={() => handleDeleteTask(task._id)}>
                     Delete
                   </button>
                 </li>
               ))}
             </ul>
           </section>
         </div>
       );
     }

     export default TaskDashboardPage;
     ```

3. **Wire into routes**
   - In `App.jsx`, replace or add route:

     ```jsx
     import TaskDashboardPage from "./TaskDashboardPage.jsx";
     import { useAuth } from "./AuthContext.jsx";
     import ProtectedRoute from "./ProtectedRoute.jsx";

     function App() {
       const { user, loading } = useAuth();
       const isLoggedIn = !!user;

       if (loading) return <p>Loading auth...</p>;

       return (
         <Routes>
           {/* login/register routes */}
           <Route
             path="/dashboard"
             element={
               <ProtectedRoute isLoggedIn={isLoggedIn}>
                 <TaskDashboardPage />
               </ProtectedRoute>
             }
           />
         </Routes>
       );
     }
     ```

4. **Test end‑to‑end**
   - Run backend + React app.
   - Login.
   - Visit `/dashboard`.
   - Create tasks, filter by status, delete tasks.

### Practice Exercises
- Show a friendly message when there are no tasks.
- Display dueDate if present, e.g. `new Date(task.dueDate).toLocaleDateString()`.

### Reflection
In `notes-week10-day3.md`:

- How similar was building the Task Dashboard to your Notes Dashboard?
- What part of the flow (React → API → DB → React) felt clearest? Which felt most fragile?

---

## Day 4 – Editing Tasks & Status Workflows (Frontend + Backend Reuse)

### Goals
- Implement **editing tasks** (title, description, status).
- Add **status toggle buttons** (e.g., mark as “in-progress”, then “done”).
- Improve the UI around task updates.

### Concepts
- Reusing `updateTaskApi`.
- Thinking about “workflow”:
  - `todo` → `in-progress` → `done`.
- Inline editing vs form-based editing; pick a simple pattern.

### Step‑by‑Step Tasks

1. **Add status controls to each task item**
   - In `TaskDashboardPage`, modify task render:

     ```jsx
     {tasks.map((task) => (
       <li key={task._id}>
         <strong>{task.title}</strong>{" "}
         <span>({task.status || "todo"})</span>
         {task.description && <p>{task.description}</p>}

         <div>
           {task.status !== "todo" && (
             <button onClick={() => handleUpdateTask(task._id, { status: "todo" })}>
               Set Todo
             </button>
           )}
           {task.status !== "in-progress" && (
             <button
               onClick={() =>
                 handleUpdateTask(task._id, { status: "in-progress" })
               }
             >
               Set In-Progress
             </button>
           )}
           {task.status !== "done" && (
             <button onClick={() => handleUpdateTask(task._id, { status: "done" })}>
               Set Done
             </button>
           )}
           <button onClick={() => startEdit(task)}>Edit</button>
           <button onClick={() => handleDeleteTask(task._id)}>Delete</button>
         </div>
       </li>
     ))}
     ```

2. **Implement `handleUpdateTask`**
   - In `TaskDashboardPage`:

     ```jsx
     import { updateTaskApi } from "./api.js";

     async function handleUpdateTask(id, updates) {
       try {
         await updateTaskApi(id, updates);
         loadTasks();
       } catch (err) {
         setError(err.message);
       }
     }
     ```

3. **Add an “editing mode” for a single task (simple approach)**
   - At top of component state:

     ```jsx
     const [editingTask, setEditingTask] = useState(null);
     ```

   - `startEdit(task)`:

     ```jsx
     function startEdit(task) {
       setEditingTask(task);
       setTitle(task.title);
       setDescription(task.description || "");
       // optionally set status input etc.
       window.scrollTo({ top: 0, behavior: "smooth" });
     }
     ```

   - Modify the create form to handle both create & update:

     ```jsx
     async function handleAddOrUpdateTask(e) {
       e.preventDefault();
       const trimmedTitle = title.trim();
       const trimmedDescription = description.trim();
       if (!trimmedTitle) return;

       try {
         if (editingTask) {
           await updateTaskApi(editingTask._id, {
             title: trimmedTitle,
             description: trimmedDescription,
           });
         } else {
           await createTaskApi({
             title: trimmedTitle,
             description: trimmedDescription,
           });
         }

         setTitle("");
         setDescription("");
         setEditingTask(null);
         loadTasks();
       } catch (err) {
         setError(err.message);
       }
     }
     ```

   - Adjust form button text:

     ```jsx
     <button type="submit">
       {editingTask ? "Update Task" : "Add Task"}
     </button>
     {editingTask && (
       <button
         type="button"
         onClick={() => {
           setEditingTask(null);
           setTitle("");
           setDescription("");
         }}
       >
         Cancel Edit
       </button>
     )}
     ```

### Practice Exercises
- Add a `select` for status in the form; when editing, pre‑fill it with the task’s status and send it in `updateTaskApi`.
- Visually differentiate `done` tasks (e.g., strike‑through title or gray color).

### Reflection
In `notes-week10-day4.md`:

- Which approach to editing felt clearer: inline buttons or using the main form in “edit mode”? Why?
- How did you avoid editing the wrong task?

---

## Day 5 – UX Polish: Loading, Errors, Empty States, and Layout

### Goals
- Make the Task Dashboard feel more like a product:
  - Clear loading indicators
  - Friendly empty states
  - Consistent error messages
  - Cleaner layout

### Concepts
- Componentizing UI patterns:
  - `<Loading />`, `<ErrorMessage />`, `<EmptyState />`.
- Making UI communicate state clearly.

### Step‑by‑Step Tasks

1. **Create small reusable UI components**
   - `src/ui/Loading.jsx`:

     ```jsx
     function Loading({ message = "Loading..." }) {
       return <p>{message}</p>;
     }
     export default Loading;
     ```

   - `src/ui/ErrorMessage.jsx`:

     ```jsx
     function ErrorMessage({ message }) {
       if (!message) return null;
       return <p style={{ color: "red" }}>Error: {message}</p>;
     }
     export default ErrorMessage;
     ```

   - `src/ui/EmptyState.jsx`:

     ```jsx
     function EmptyState({ message }) {
       return <p>{message}</p>;
     }
     export default EmptyState;
     ```

2. **Use them in `TaskDashboardPage`**
   - Replace inline `<p>Loading tasks...</p>` etc. with these components:

     ```jsx
     import Loading from "./ui/Loading.jsx";
     import ErrorMessage from "./ui/ErrorMessage.jsx";
     import EmptyState from "./ui/EmptyState.jsx";

     // ...

     {error && <ErrorMessage message={error} />}
     {loading && <Loading message="Loading tasks..." />}

     {!loading && !error && tasks.length === 0 && (
       <EmptyState message="No tasks yet. Add your first task!" />
     )}
     ```

3. **Improve layout with basic CSS**
   - In `index.css`, add:

     ```css
     body {
       font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
         sans-serif;
       margin: 0;
       background: #f4f4f4;
     }

     .app-shell {
       max-width: 900px;
       margin: 0 auto;
       padding: 20px;
     }

     header {
       border-bottom: 1px solid #ddd;
       margin-bottom: 16px;
       padding-bottom: 8px;
     }

     form {
       display: flex;
       flex-direction: column;
       gap: 8px;
       margin-bottom: 16px;
     }

     input,
     textarea,
     select,
     button {
       font: inherit;
     }

     ul {
       list-style: none;
       padding: 0;
     }

     li {
       background: white;
       padding: 8px 12px;
       margin-bottom: 8px;
       border-radius: 4px;
       box-shadow: 0 1px 2px rgba(0, 0, 0, 0.06);
     }

     li.done-task strong {
       text-decoration: line-through;
       color: #888;
     }
     ```

   - Wrap your main content in `App` with `<div className="app-shell">`.

4. **Apply small micro‑copy improvements**
   - Make messages more user‑friendly, e.g.:
     - “No tasks yet. What’s the first thing you want to accomplish today?”
     - “We couldn’t load your tasks. Check your connection and try again.”

### Practice Exercises
- Add a small “Last Updated: HH:MM” text after tasks load.
- Disable the “Add/Update Task” button when the title is empty or while a request is in progress.

### Reflection
In `notes-week10-day5.md`:

- Which small UX change felt most impactful to you as a user?
- If you showed your dashboard to a friend, which part do you think they’d find confusing?

---

## Day 6 – Simple Custom Hooks & Code Cleanup

### Goals
- Extract **reusable logic** into a custom hook (`useTasks`).
- Make your components smaller and more focused.
- Do a light **refactor and cleanup**.

### Concepts
- Custom hooks:
  - Just functions that use other hooks and encapsulate logic.
- Separation of concerns:
  - `TaskDashboardPage` focuses on layout & orchestration.
  - `useTasks` handles state + API details.

### Step‑by‑Step Tasks

1. **Create `useTasks` hook**
   - `src/hooks/useTasks.js`:

     ```jsx
     import { useCallback, useEffect, useState } from "react";
     import {
       fetchTasksApi,
       createTaskApi,
       deleteTaskApi,
       updateTaskApi,
       fetchTaskStatsApi,
     } from "../api.js";

     export function useTasks() {
       const [tasks, setTasks] = useState([]);
       const [stats, setStats] = useState(null);
       const [loading, setLoading] = useState(false);
       const [error, setError] = useState("");

       const loadTasks = useCallback(async (filters = {}) => {
         setLoading(true);
         setError("");
         try {
           const data = await fetchTasksApi(filters);
           setTasks(data);
           const s = await fetchTaskStatsApi();
           setStats(s);
         } catch (err) {
           setError(err.message);
         } finally {
           setLoading(false);
         }
       }, []);

       async function addTask(taskData) {
         setError("");
         try {
           await createTaskApi(taskData);
           await loadTasks();
         } catch (err) {
           setError(err.message);
           throw err;
         }
       }

       async function removeTask(id) {
         setError("");
         try {
           await deleteTaskApi(id);
           await loadTasks();
         } catch (err) {
           setError(err.message);
           throw err;
         }
       }

       async function updateTask(id, updates) {
         setError("");
         try {
           await updateTaskApi(id, updates);
           await loadTasks();
         } catch (err) {
           setError(err.message);
           throw err;
         }
       }

       // Initial load
       useEffect(() => {
         loadTasks();
       }, [loadTasks]);

       return {
         tasks,
         stats,
         loading,
         error,
         loadTasks,
         addTask,
         removeTask,
         updateTask,
       };
     }
     ```

2. **Refactor `TaskDashboardPage` to use `useTasks`**
   - Replace internal state/logic with hook usage:

     ```jsx
     import { useTasks } from "./hooks/useTasks.js";

     function TaskDashboardPage() {
       const { user, logout } = useAuth();
       const {
         tasks,
         stats,
         loading,
         error,
         loadTasks,
         addTask,
         removeTask,
         updateTask,
       } = useTasks();

       const [title, setTitle] = useState("");
       const [description, setDescription] = useState("");
       const [statusFilter, setStatusFilter] = useState("");
       const [sort, setSort] = useState("newest");
       const [editingTask, setEditingTask] = useState(null);

       async function handleSubmit(e) {
         e.preventDefault();
         const trimmedTitle = title.trim();
         const trimmedDescription = description.trim();
         if (!trimmedTitle) return;

         const payload = {
           title: trimmedTitle,
           description: trimmedDescription,
         };

         try {
           if (editingTask) {
             await updateTask(editingTask._id, payload);
           } else {
             await addTask(payload);
           }
           setTitle("");
           setDescription("");
           setEditingTask(null);
         } catch (err) {
           // error already set in hook
         }
       }

       async function handleFilterChange(newStatus, newSort) {
         setStatusFilter(newStatus);
         setSort(newSort);
         await loadTasks({
           status: newStatus || undefined,
           sort: newSort === "newest" ? undefined : newSort,
         });
       }

       // Task list rendering unchanged, but uses removeTask/updateTask
     }
     ```

   - Keep your list + edit logic, but call `updateTask(id, updates)` and `removeTask(id)`.

3. **Clean up**
   - Check for:
     - Unused imports.
     - Duplicated API logic now covered by `useTasks`.
   - Add small comments where logic may be confusing.

### Practice Exercises
- Think of another feature that could benefit from a hook later (e.g., `useAuth` is already a hook via context).
- Rename one or two functions/variables to make their intent clearer.

### Reflection
In `notes-week10-day6.md`:

- What did moving logic to `useTasks` change in how `TaskDashboardPage` feels?
- In your own words, what makes a good custom hook?

---

## Day 7 – End‑to‑End Review & Small Feature of Your Choice

### Goals
- Review the complete stack: **React + API + Mongo + Auth + Tasks**.
- Implement one small feature that matters to you.
- Reflect on what you can now build on your own.

### Concepts
- End‑to‑end thinking:
  - Data flows and responsibilities at each layer.
- Feature design: slice off something small and complete it.

### Step‑by‑Step Tasks

1. **Written end‑to‑end walkthrough**
   - In `notes-week10-day7.md`, describe:

     - When you click “Add Task”:
       - What happens in React (state, hook)?
       - What request hits the API (method, URL)?
       - What happens in Express & Mongoose?
       - How the updated data gets back to React.

2. **Pick one small feature to implement fully**
   Some ideas (pick ONE):

   - **Due dates & overdue highlighting**
     - Use `dueDate` in Task schema (already present).
     - Frontend: add a date input when creating/editing tasks.
     - Show due date in list and highlight tasks past due.

   - **Quick search box (client‑side)**
     - Add text input above the list.
     - Filter displayed tasks in React by title/description.

   - **Group tasks by status**
     - Separate lists/sections for “Todo”, “In‑Progress”, “Done”.
     - Maybe simple counts per section.

3. **Implement chosen feature end‑to‑end**
   - Think first: write a 5‑line spec in your notes:
     - What does the user do?
     - What changes in DB (if any)?
     - What changes in API?
     - What changes in React UI?

   - Then implement:
     - Backend changes (if needed: new fields or queries).
     - API client updates.
     - React UI updates + event handlers.
   - Test thoroughly:
     - With different users.
     - On reload.
     - With error scenarios (e.g., invalid date).

4. **Run a mini self‑demo**
   - Imagine you’re showing your Task Manager to someone:
     - Walk through login, creating tasks, changing status, filtering, your new feature.
   - Note anything that feels clunky and (if time) fix at least one thing.

### Practice Exercises
- Add your new feature’s edge cases to your personal test checklist (from earlier weeks).

### Reflection – End of Week 10
In `notes-week10-day7.md`, also answer:

- 3 things you now feel confident about (e.g., “I can create new models in Mongoose and hook them to React”).
- 2 areas you still find confusing (e.g., “useEffect dependency arrays”, “error handling patterns”).
- 1 feature you’d like to add if you had another week on this Task Manager.

---

By the end of Week 10 you should:

- Have a **full‑stack authenticated Task Manager**:
  - Users register & log in.
  - Tasks are created, edited, deleted, and filtered per user.
  - Tasks have status and (optionally) due dates + stats.
- Be comfortable:
  - Designing Mongo models and Express routes.
  - Writing a React frontend that consumes a protected API.
  - Adding small features end‑to‑end with tests and UX polish.

From here you can move toward:

- More robust testing (backend + frontend)
- Deployment of backend (Render/Railway) and frontend (Vercel/Netlify)
- Or start planning your **capstone app** for Weeks 11–12.
