### Week 12 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 12:**  
Build and ship your **Capstone App** – a small but real full‑stack product that ties together everything you’ve learned:

- Design your own app (not just follow a tutorial)
- Implement backend (Express + Mongo + JWT)
- Implement frontend (React + Auth + API integration)
- Deploy and document it as a portfolio piece

I’ll anchor this plan around a **Habit Tracker** example, but you can swap in another idea (Expense Tracker, Mini Blog, etc.). Wherever you see “Habit”, mentally substitute your app’s main object (e.g. “Expense” or “Post”).

---

## Day 1 – Choose Capstone & Define Scope

### Goals
- Choose your capstone idea.
- Clearly define **scope** (must‑have vs nice‑to‑have).
- Sketch data models and main API endpoints.
- Set up repos and base project structure (by reusing your Task Manager code).

### Concepts
- Product thinking: user stories, MVP vs v2.
- Translating user stories to models and endpoints.
- Reusing your existing code as a starter.

### Step‑by‑Step Tasks

1. **Pick your capstone idea (decide and commit)**  
   In `notes-week12-day1.md`:

   Choose ONE and stick with it this week:

   - Habit Tracker  
     Users define habits (e.g. “Exercise”) and log completions per day. See streaks and weekly stats.
   - Expense Tracker  
     Users log expenses (amount, category, date), see totals per period, maybe simple charts.
   - Mini Blog / Article Platform  
     Users write posts, edit/delete them, and see a list of their own posts.

   Write a 2–3 sentence **problem statement**:
   - “This app helps users … by letting them … so that …”

2. **Write 6–10 user stories**
   Still in `notes-week12-day1.md`, in plain language, e.g. for Habit Tracker:

   - “As a user, I can register and log in so my data is private.”
   - “As a user, I can create a habit with a name and description.”
   - “As a user, I can mark a habit as done for a given day.”
   - “As a user, I can see my completion history for the last 7 days.”
   - “As a user, I can see a streak count for each habit.”

   Split them into:

   - **Must‑have (MVP)**: 4–6 stories.
   - **Nice‑to‑have**: 2–4 stories (only do after MVP works).

3. **Sketch data models**
   Still in notes, design Mongo models (no code yet). For a Habit Tracker example:

   - `User` (reuse from Task Manager)
   - `Habit`:
     - `name: String, required`
     - `description: String`
     - `frequency: String` (e.g. `"daily"`, `"weekly"`)
     - `user: ObjectId (ref User)`
     - `archived: Boolean`
   - `HabitLog`:
     - `habit: ObjectId (ref Habit)`
     - `date: Date` (day of completion)
     - `note: String?`

   Do the same exercise for your chosen app.

4. **List main API endpoints you’ll need**
   For Habit Tracker (example):

   - `POST /api/habits` – create habit
   - `GET /api/habits` – list user’s habits
   - `PATCH /api/habits/:id` – update habit (name, description, archived)
   - `DELETE /api/habits/:id` – optional, or soft delete
   - `POST /api/habits/:id/logs` – log completion for a date (default: today)
   - `GET /api/habits/:id/logs?from=...&to=...` – get history for a habit
   - `GET /api/habits/stats/summary` – summary stats (e.g. counts, streaks)

5. **Set up project repos & base code**
   - **Backend**:
     - Duplicate your Task Manager backend folder as `week12-capstone-api` (or similar).
     - Change names in `package.json`, `README.md`.
     - Keep:
       - `User` model
       - auth routes + middleware
       - DB connection, error handlers, etc.
     - You’ll add new models & routes for your domain.
   - **Frontend**:
     - Duplicate your Task Manager React client as `week12-capstone-client`.
     - Change app title, branding.
     - Keep:
       - `AuthContext`
       - API base logic (`api.js`)
       - Login/Register pages
     - You’ll add new pages and components for your domain.

### Practice Exercises
- Identify one user story you could **drop** if you’re tight on time (to keep scope realistic).
- Identify one **stretch** user story that would be great but not essential.

### Reflection
In `notes-week12-day1.md`:

- Write 3 sentences describing:
  - Who this app is for.
  - What success looks like for the MVP (what MUST be working by Day 7).

---

## Day 2 – Backend: Models & Routes Skeleton

### Goals
- Implement Mongoose models for your capstone.
- Create **route skeletons** (no complex logic yet).
- Ensure auth is already wired (reuse from Task Manager).

### Concepts
- Translating model sketches to schemas.
- Planning routes before filling in details.
- Reusing patterns (80–20) from your Task Manager API.

### Step‑by‑Step Tasks

1. **Create models**
   - In `week12-capstone-api/models/`, add your new models, e.g. `Habit.js`, `HabitLog.js` or `Expense.js`, etc.
   - Use your Week 10 Task model as a reference.
   - Example `Habit`:

     ```js
     const mongoose = require("mongoose");

     const habitSchema = new mongoose.Schema(
       {
         name: {
           type: String,
           required: [true, "Name is required"],
           trim: true,
         },
         description: {
           type: String,
           trim: true,
           default: "",
         },
         frequency: {
           type: String,
           enum: ["daily", "weekly"],
           default: "daily",
         },
         user: {
           type: mongoose.Schema.Types.ObjectId,
           ref: "User",
           required: true,
         },
         archived: {
           type: Boolean,
           default: false,
         },
       },
       { timestamps: true }
     );

     module.exports = mongoose.model("Habit", habitSchema);
     ```

   - Example `HabitLog`:

     ```js
     const habitLogSchema = new mongoose.Schema(
       {
         habit: {
           type: mongoose.Schema.Types.ObjectId,
           ref: "Habit",
           required: true,
         },
         date: {
           type: Date,
           required: true,
         },
         note: {
           type: String,
           trim: true,
           default: "",
         },
       },
       { timestamps: true }
     );

     module.exports = mongoose.model("HabitLog", habitLogSchema);
     ```

   Adapt this for your domain.

2. **Create routes skeleton**
   - In `routes/`, create something like:
     - `habitRoutes.js` (or `expenseRoutes.js`, etc.)
   - Include auth middleware at top:

     ```js
     const express = require("express");
     const authMiddleware = require("../middleware/auth");
     const Habit = require("../models/Habit");
     const HabitLog = require("../models/HabitLog");

     const router = express.Router();

     router.use(authMiddleware);

     // TODO: fill implementations
     router.get("/", async (req, res, next) => {
       // list habits
     });

     router.post("/", async (req, res, next) => {
       // create habit
     });

     router.patch("/:id", async (req, res, next) => {
       // update habit
     });

     router.delete("/:id", async (req, res, next) => {
       // delete/archive habit
     });

     router.post("/:id/logs", async (req, res, next) => {
       // log completion
     });

     router.get("/:id/logs", async (req, res, next) => {
       // get logs for a date range
     });

     router.get("/stats/summary", async (req, res, next) => {
       // stats
     });

     module.exports = router;
     ```

3. **Wire routes in server**
   - In `server.js`:

     ```js
     const habitRoutes = require("./routes/habitRoutes");
     app.use("/api/habits", habitRoutes);
     ```

4. **Run server**
   - Create `.env` (copy from Task Manager, but use a **new DB name** if you like).
   - Run `npm run dev` and ensure server starts without route errors.

### Practice Exercises
- For each route, write a short comment in your notes about what it should do and what it should return (shape of JSON).
- Decide now whether you will soft‑delete (e.g. set `archived: true`) vs hard delete for your main resource.

### Reflection
In `notes-week12-day2.md`:

- Did any field or endpoint feel unnecessary once you started writing code?
- Is there anything you’re adding “just in case” that you could postpone?

---

## Day 3 – Backend: Implement Core Logic & Test with Postman

### Goals
- Implement real logic for **must‑have** routes.
- Ensure auth + user scoping works (data isolated per user).
- Test all endpoints with Postman / Insomnia (no frontend yet).

### Concepts
- Core CRUD + domain‑specific logic (e.g. logging completions, computing simple stats).
- Keeping routes simple; push complex logic to small helpers when needed.

### Step‑by‑Step Tasks

1. **Implement core CRUD for main resource**
   In your routes file, implement:

   - List main resource (e.g., GET `/api/habits`):

     ```js
     router.get("/", async (req, res, next) => {
       try {
         const habits = await Habit.find({
           user: req.user._id,
           archived: false,
         }).sort({ createdAt: -1 });
         res.json(habits);
       } catch (error) {
         next(error);
       }
     });
     ```

   - Create:

     ```js
     router.post("/", async (req, res, next) => {
       try {
         const { name, description, frequency } = req.body;
         if (!name) {
           return res.status(400).json({ error: "Name is required" });
         }
         const habit = await Habit.create({
           name,
           description,
           frequency,
           user: req.user._id,
         });
         res.status(201).json(habit);
       } catch (error) {
         next(error);
       }
     });
     ```

   - Update:

     ```js
     router.patch("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const updates = req.body;
         const habit = await Habit.findOneAndUpdate(
           { _id: id, user: req.user._id },
           updates,
           { new: true, runValidators: true }
         );
         if (!habit) {
           return res.status(404).json({ error: "Habit not found" });
         }
         res.json(habit);
       } catch (error) {
         next(error);
       }
     });
     ```

   - Delete or archive:

     ```js
     router.delete("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const deleted = await Habit.findOneAndUpdate(
           { _id: id, user: req.user._id },
           { archived: true },
           { new: true }
         );
         if (!deleted) {
           return res.status(404).json({ error: "Habit not found" });
         }
         res.status(204).send();
       } catch (error) {
         next(error);
       }
     });
     ```

   Adapt this to your chosen app.

2. **Implement key domain action (e.g., logging a habit completion)**
   Example for POST `/api/habits/:id/logs`:

   ```js
   router.post("/:id/logs", async (req, res, next) => {
     try {
       const { id } = req.params;
       const { date, note } = req.body;
       const habit = await Habit.findOne({ _id: id, user: req.user._id });
       if (!habit) {
         return res.status(404).json({ error: "Habit not found" });
       }

       const logDate = date ? new Date(date) : new Date();
       // Optionally ensure one log per day per habit
       const existing = await HabitLog.findOne({
         habit: habit._id,
         date: logDate.toDateString(),
       });

       if (existing) {
         return res.status(400).json({ error: "Already logged for this day" });
       }

       const log = await HabitLog.create({
         habit: habit._id,
         date: logDate,
         note,
       });

       res.status(201).json(log);
     } catch (error) {
       next(error);
     }
   });
   ```

   Keep it as simple as you can while satisfying your user stories.

3. **Implement simple summary (optional if time)**
   - Example: `GET /api/habits/stats/summary`:
     - Count active habits.
     - Count logs in last 7 days.

4. **Test everything with Postman**
   - Flow:
     - Register → Login → Get token.
     - Use token to:
       - Create main objects (habits/expenses/posts).
       - List them.
       - Update / archive.
       - Execute key domain action (log, expense record, etc.).
   - Validate:
     - 401 without token.
     - 404 for missing resources.
     - 400 for invalid input.

### Practice Exercises
- Try a “second” user and confirm data is isolated.
- Intentionally send bad requests (missing required fields) and ensure errors are clear.

### Reflection
In `notes-week12-day3.md`:

- Which endpoint felt most complex? Why?
- Is there any logic you wish you’d kept simpler?

---

## Day 4 – Frontend: Pages & Navigation Skeleton

### Goals
- Set up React pages and routing for your capstone.
- Integrate existing auth flow (login/register) from Task Manager.
- Design the basic **page layout** for your app.

### Concepts
- Page structure: `Login`, `Register`, `Dashboard/Overview`, sub‑views if needed.
- Navigation and protected routes.
- Thinking in components early.

### Step‑by‑Step Tasks

1. **Rename/adjust your React app**
   - In `week12-capstone-client`, update:
     - `index.html` title (e.g., “Habit Tracker”).
     - Any visible app name in `App.jsx`, headers, etc.

2. **Create page components**
   In `src/pages/` create (or adapt):

   - `LoginPage.jsx`
   - `RegisterPage.jsx`
   - `DashboardPage.jsx` (for your main resource)
   - (Optional) `SettingsPage.jsx` or `ProfilePage.jsx` (for later)

   Keep them simple initially: headings and placeholder text.

3. **Set up routes**
   - Ensure `main.jsx` wraps `App` with `BrowserRouter` and `AuthProvider` (from your Task Manager project).
   - In `App.jsx`:

     ```jsx
     import { Routes, Route, Navigate, Link } from "react-router-dom";
     import { useAuth } from "./AuthContext.jsx";
     import LoginPage from "./pages/LoginPage.jsx";
     import RegisterPage from "./pages/RegisterPage.jsx";
     import DashboardPage from "./pages/DashboardPage.jsx";
     import ProtectedRoute from "./ProtectedRoute.jsx";

     function App() {
       const { user, loading } = useAuth();
       const isLoggedIn = !!user;

       if (loading) return <p>Loading auth...</p>;

       return (
         <div className="app-shell">
           <nav>
             <Link to="/">Home</Link>{" "}
             {isLoggedIn ? (
               <>
                 | <Link to="/dashboard">Dashboard</Link>
               </>
             ) : (
               <>
                 | <Link to="/login">Login</Link> |{" "}
                 <Link to="/register">Register</Link>
               </>
             )}
           </nav>

           <Routes>
             <Route path="/" element={<Navigate to={isLoggedIn ? "/dashboard" : "/login"} />} />
             <Route path="/login" element={<LoginPage />} />
             <Route path="/register" element={<RegisterPage />} />
             <Route
               path="/dashboard"
               element={
                 <ProtectedRoute isLoggedIn={isLoggedIn}>
                   <DashboardPage />
                 </ProtectedRoute>
               }
             />
           </Routes>
         </div>
       );
     }

     export default App;
     ```

4. **Design Dashboard layout (UI only, no API yet)**
   - In `DashboardPage.jsx`, sketch sections, e.g.:

     For Habit Tracker:

     - Top bar: current user email + logout button.
     - Left or top: “Create Habit” form.
     - Right or below: list of habits.
     - Optional bottom: stats / graphs / recent logs.

   Use placeholders. Tomorrow you’ll wire to API.

### Practice Exercises
- Draw a small wireframe (on paper or notes) of your Dashboard – boxes for sections, rough labels.
- Decide how many main screens you really need (avoid over‑complicating).

### Reflection
In `notes-week12-day4.md`:

- Does your page layout make sense from a user’s perspective?
- If you had to explain the main screen to a non‑developer, what would you say?

---

## Day 5 – Frontend: Hook Up Core Flows to API

### Goals
- Integrate React with your new backend endpoints.
- Implement the **MVP flows** for your main resource.
- Handle loading and error states.

### Concepts
- Reusing API patterns from Task Manager:
  - `api.js` with `fetchXxxApi` functions.
- `useEffect` + state for initial loads.
- Controlled forms for create/update.

### Step‑by‑Step Tasks

1. **Extend `api.js` in React project**
   Similar to Week 10 Task API, but for your capstone domain.

   For Habit Tracker example (`src/api.js`):

   ```js
   const API_BASE = import.meta.env.VITE_API_BASE_URL || "http://localhost:3000";

   let authToken = null;
   export function setAuthToken(token) {
     authToken = token;
   }

   function getAuthHeaders() {
     const headers = { "Content-Type": "application/json" };
     if (authToken) headers.Authorization = `Bearer ${authToken}`;
     return headers;
   }

   async function handleResponse(res) {
     let data = null;
     try {
       if (res.status !== 204) data = await res.json();
     } catch (e) {}
     if (!res.ok) {
       const message = data?.error || `Request failed with status ${res.status}`;
       throw new Error(message);
     }
     return data;
   }

   export async function fetchHabitsApi() {
     const res = await fetch(`${API_BASE}/api/habits`, {
       headers: getAuthHeaders(),
     });
     return handleResponse(res);
   }

   export async function createHabitApi({ name, description, frequency }) {
     const res = await fetch(`${API_BASE}/api/habits`, {
       method: "POST",
       headers: getAuthHeaders(),
       body: JSON.stringify({ name, description, frequency }),
     });
     return handleResponse(res);
   }

   export async function updateHabitApi(id, updates) {
     const res = await fetch(`${API_BASE}/api/habits/${id}`, {
       method: "PATCH",
       headers: getAuthHeaders(),
       body: JSON.stringify(updates),
     });
     return handleResponse(res);
   }

   export async function deleteHabitApi(id) {
     const res = await fetch(`${API_BASE}/api/habits/${id}`, {
       method: "DELETE",
       headers: getAuthHeaders(),
     });
     return handleResponse(res);
   }

   export async function logHabitApi(id, payload = {}) {
     const res = await fetch(`${API_BASE}/api/habits/${id}/logs`, {
       method: "POST",
       headers: getAuthHeaders(),
       body: JSON.stringify(payload),
     });
     return handleResponse(res);
   }
   ```

   Adapt this to your domain.

2. **Use auth token from `AuthContext`**
   - Ensure your `AuthContext` calls `setAuthToken(token)` on login and also when loading stored token, exactly as in Weeks 9–10.

3. **Implement core flows in `DashboardPage`**
   For example:

   - State & loading:

     ```jsx
     const [habits, setHabits] = useState([]);
     const [loading, setLoading] = useState(false);
     const [error, setError] = useState("");
     const [name, setName] = useState("");
     const [description, setDescription] = useState("");
     const [frequency, setFrequency] = useState("daily");
     ```

   - Load habits on mount:

     ```jsx
     useEffect(() => {
       async function loadHabits() {
         setLoading(true);
         setError("");
         try {
           const data = await fetchHabitsApi();
           setHabits(data);
         } catch (err) {
           setError(err.message);
         } finally {
           setLoading(false);
         }
       }
       loadHabits();
     }, []);
     ```

   - Create habit:

     ```jsx
     async function handleCreateHabit(e) {
       e.preventDefault();
       const trimmedName = name.trim();
       if (!trimmedName) return;
       try {
         await createHabitApi({ name: trimmedName, description, frequency });
         setName("");
         setDescription("");
         setFrequency("daily");
         const data = await fetchHabitsApi();
         setHabits(data);
       } catch (err) {
         setError(err.message);
       }
     }
     ```

   - Log completion:

     ```jsx
     async function handleLogToday(habitId) {
       try {
         await logHabitApi(habitId, {}); // default today
         // optionally reload or update local state
       } catch (err) {
         setError(err.message);
       }
     }
     ```

   - Render list with buttons:

     ```jsx
     <ul>
       {habits.map((habit) => (
         <li key={habit._id}>
           <strong>{habit.name}</strong> ({habit.frequency})
           {habit.description && <p>{habit.description}</p>}
           <button onClick={() => handleLogToday(habit._id)}>Log Today</button>
           <button onClick={() => handleDeleteHabit(habit._id)}>Archive</button>
         </li>
       ))}
     </ul>
     ```

     (Implement `handleDeleteHabit` with `deleteHabitApi` and reload.)

4. **Test MVP flows in the browser**
   - Register & login via frontend.
   - Create some habits (or your chosen resource type).
   - Perform the key action (log completion / add expense / create post).
   - Fix any errors you hit (check Network + console + backend logs).

### Practice Exercises
- Add simple `Loading` and `ErrorMessage` components (like in Week 10) and use them here.
- Add a message “No habits yet” when the list is empty.

### Reflection
In `notes-week12-day5.md`:

- What was the toughest part of wiring frontend to your new API?
- Did prior Task Manager code help, or did you feel tempted to rewrite?

---

## Day 6 – Enhancements, Stats & Deployment

### Goals
- Add one or two **small but meaningful enhancements** (stats, filters, better UX).
- Deploy backend and frontend (reusing your Week 11 knowledge).
- Connect live frontend to live backend.

### Concepts
- Enhancing value without exploding scope.
- Using existing deployment pipelines.
- Keeping environment‑specific config in env vars.

### Step‑by‑Step Tasks

1. **Choose 1–2 enhancements**
   From your Day 1 “nice‑to‑have” list, pick **only 1–2** that:
   - Are clearly valuable to a user.
   - Are not huge rewrites.

   Examples for Habit Tracker:
   - Show a **7‑day completion calendar** per habit.
   - Show total completions and current streak.
   - Filter habits by frequency or archived vs active.

   For Expense Tracker:
   - Sum expenses per category for this month.
   - Filter by date range.

2. **Backend: implement minimal stats endpoint if needed**
   Example: `GET /api/habits/stats/summary`:

   ```js
   router.get("/stats/summary", async (req, res, next) => {
     try {
       const userId = req.user._id;
       const totalHabits = await Habit.countDocuments({ user: userId, archived: false });
       const totalLogs = await HabitLog.countDocuments({ habit: { $exists: true } });
       // You can refine: last 7 days, etc.

       res.json({ totalHabits, totalLogs });
     } catch (error) {
       next(error);
     }
   });
   ```

   Adapt to your app; keep queries simple.

3. **Frontend: display the stats**
   - Add `fetchHabitsStatsApi` in `api.js` and call it from your dashboard.
   - Show a small stats section: “You have X active habits and Y total completions” (or similar).

4. **Deploy backend (if separate from Task Manager)**
   - Push to GitHub (if not already).
   - Create a new Render/Railway service OR reuse the same one if you merged code:
     - Set `MONGODB_URI`, `JWT_SECRET`, etc.
     - Set start command: `npm start`.
   - Test live API with Postman.

5. **Deploy frontend**
   - Use Vercel/Netlify:
     - Set `VITE_API_BASE_URL` to your new backend URL.
   - Build and deploy.
   - Update CORS on backend to include this new frontend origin.

6. **Test live stack**
   - Visit live frontend URL.
   - Register, login, create main resources, perform key action.
   - Confirm data is in live DB (Atlas).

### Practice Exercises
- Compare local vs production behavior; note any differences and hypotheses why.
- For each difference, decide if it must be fixed now or can be documented as “known issue”.

### Reflection
In `notes-week12-day6.md`:

- Which enhancement added the most value for least effort?
- Did deployment go smoother this time than Week 11? Why or why not?

---

## Day 7 – Polishing, Documentation & Final Review

### Goals
- Polish UX and fix obvious bugs.
- Write clear **documentation** and a short **portfolio description**.
- Reflect on your 12‑week journey and plan next steps.

### Concepts
- “Last 10%” polish: labels, messages, responsiveness, edge cases.
- Treating your app as something you’d show to others.

### Step‑by‑Step Tasks

1. **Run your full E2E checklist on the capstone**
   - Using **production URLs only**, run through:
     - Registration
     - Login/Logout
     - Main resource CRUD
     - Key domain action (log, expense add, publish post)
     - Any stats or filters
   - Note any issues in `notes-week12-day7.md`.

2. **Fix 2–3 top issues**
   - Prioritize:
     - Anything that breaks a main flow (high severity).
     - Anything that makes the app confusing to use.
   - Implement small UI tweaks:
     - Clearer button labels.
     - Disabled buttons on submit.
     - Better error messages.

3. **Tidy UI & responsiveness**
   - Check app on mobile viewport (DevTools device mode).
   - Fix:
     - Horizontal scrolling.
     - Very tiny text.
     - Buttons too close together.
   - Adjust CSS as needed.

4. **Write README & portfolio blurb**

   - **Backend README**:
     - Short description (what this API does).
     - Tech stack (Node, Express, MongoDB, JWT, etc.).
     - How to:
       - Run locally (env vars, commands).
       - Run tests (if any).
     - List main endpoints (auth + domain).

   - **Frontend README**:
     - Description of the app (for users).
     - Tech stack (React, Vite, React Router).
     - How to:
       - Run locally (env vars, commands).
     - Link to live demo.

   - **Portfolio blurb** (in `notes-week12-day7.md`, to later paste in your site/LinkedIn):

     A short paragraph like:

     > “HabitTrack is a full‑stack habit tracking app built with React, Node.js, Express, and MongoDB. Authenticated users can create custom habits, log daily completions, and see simple stats over time. I focused on clean RESTful APIs, JWT‑based authentication, and a responsive React UI that consumes the API. Live demo: …, Source: …”

     Adapt for your app.

5. **Final reflection on the 12 weeks**
   In `notes-week12-day7.md`, write:

   - What I can build now (be concrete: “I can build an authenticated CRUD app with React + Node + Mongo, deploy it, and document it.”).
   - What still feels weak (e.g., testing, performance, advanced UI, TypeScript).
   - What I want to learn next (e.g., TypeScript, Next.js, advanced auth, testing, design systems).

### Practice Exercises
- Show your app to one friend or colleague:
  - Ask them to complete 2–3 tasks (e.g., “create two habits and log today”).
  - Watch silently and take notes on where they struggle or hesitate.
  - Fix at least one thing based on their feedback.

### Optional Stretch
- Add the capstone to your existing portfolio from Week 2:
  - Screenshot(s).
  - Short description.
  - Links to live demo and GitHub repos.

---

### After Week 12

You now have:

- A **deployed, documented full‑stack app** that you designed yourself.
- Experience taking an idea from user stories → models → API → React UI → deployment.
- A clear sense of:
  - What you can already do.
  - Where you want to deepen (testing, UI/UX, performance, etc.).

Next natural steps:

- Build 1–2 more small apps (or extend this one) focusing on your weaker areas.
- Start applying for internships/junior roles or doing small freelance projects.
- Keep improving your portfolio and GitHub with real, maintained code.

If you’d like, you can now share which capstone idea you chose, and I can suggest 2–3 specific improvements or stretch goals tailored to that app.
