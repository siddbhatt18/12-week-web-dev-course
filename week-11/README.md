### Week 11 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 11:**  
Make your Task Manager **production‑ready**:

- Add **backend tests** (Jest + Supertest)
- Create **manual end‑to‑end test flows**
- Refactor and clean up code
- **Deploy backend** (Render/Railway) and **frontend** (Vercel/Netlify)
- Document how to run and use your app

Assume you’re starting from:

- Backend: Node/Express + Mongo + JWT auth + `/api/tasks` (and maybe notes)
- Frontend: React (Vite) client using that API
- CORS already configured for local dev (`http://localhost:5173`)

You’ll end this week with:

- A **live API URL**
- A **live React app URL**
- Basic tests and documentation

---

## Day 1 – Backend Testing Setup (Jest & Supertest, Simple Tests)

### Goals
- Set up **Jest** for backend testing.
- Write a couple of **unit tests** for small pieces of logic.
- Write your first **API test** with Supertest.

### Concepts
- Test types:
  - Unit tests: small, isolated (functions, helpers).
  - Integration tests: test API endpoints, DB interactions.
- Jest:
  - `describe`, `it/test`, `expect`.
- Supertest:
  - Simulate HTTP requests against your Express app.

### Step‑by‑Step Tasks

1. **Install Jest & Supertest**
   - In your backend project folder:

     ```bash
     npm install --save-dev jest supertest
     ```

   - If you use ES modules you’d configure slightly differently; assume CommonJS for now.

2. **Add Jest config in `package.json`**
   - In `package.json`:

     ```json
     "scripts": {
       "dev": "nodemon server.js",
       "start": "node server.js",
       "test": "jest --runInBand"
     },
     "jest": {
       "testEnvironment": "node"
     }
     ```

   - `--runInBand` avoids parallel DB tests fighting each other.

3. **Expose your Express app without starting the server**
   - In `server.js`, refactor slightly:

     ```js
     require("dotenv").config();
     const express = require("express");
     const connectDB = require("./config/db");

     const app = express();
     app.use(express.json());
     // cors, routes, error handlers…

     if (require.main === module) {
       // If run directly: connect DB + listen
       const PORT = process.env.PORT || 3000;
       (async () => {
         await connectDB(process.env.MONGODB_URI);
         app.listen(PORT, () => {
           console.log(`Server running on http://localhost:${PORT}`);
         });
       })();
     }

     module.exports = app;
     ```

   - This way tests can `require("./server")` without starting the HTTP listener.

4. **Create a simple utility function to test**
   - For a gentle start: `utils/formatTaskTitle.js`:

     ```js
     function formatTaskTitle(title) {
       if (!title) return "";
       return title.trim().replace(/\s+/g, " ");
     }

     module.exports = formatTaskTitle;
     ```

   - Create `__tests__/formatTaskTitle.test.js`:

     ```js
     const formatTaskTitle = require("../utils/formatTaskTitle");

     describe("formatTaskTitle", () => {
       it("trims whitespace", () => {
         expect(formatTaskTitle("  Hello  ")).toBe("Hello");
       });

       it("collapses multiple spaces", () => {
         expect(formatTaskTitle("Hello   world")).toBe("Hello world");
       });

       it("returns empty string for falsy input", () => {
         expect(formatTaskTitle("")).toBe("");
         expect(formatTaskTitle(null)).toBe("");
       });
     });
     ```

   - Run:

     ```bash
     npm test
     ```

5. **First API test with Supertest**
   - Create `__tests__/status.test.js` (or tasks):

     ```js
     const request = require("supertest");
     const app = require("../server");

     describe("Basic API health", () => {
       it("returns 404 for unknown route", async () => {
         const res = await request(app).get("/api/unknown-route");
         expect(res.status).toBe(404);
         expect(res.body).toHaveProperty("error");
       });
     });
     ```

   - Run `npm test` again.

### Practice Exercises
- Add another small util function (e.g., `utils/isOverdue(dueDate)`) and write tests for it.
- Try changing a test to an incorrect expected value and watch Jest fail; read the failure output carefully.

### Reflection
Create `notes-week11-day1.md`:

- What’s the difference between unit and integration tests?
- How does Jest help you catch regressions?

### Optional Stretch
- Add a simple `/api/health` route that returns `{ status: "ok" }` and write a Supertest for it.

---

## Day 2 – Backend Integration Tests (Auth + Tasks with a Test DB)

### Goals
- Write **integration tests** that hit real endpoints.
- Use a separate **test database**.
- Test core flows: register → login → create task → list tasks.

### Concepts
- Test DB vs dev DB:
  - Use a different `MONGODB_URI` for tests.
- Jest lifecycle hooks:
  - `beforeAll`, `afterAll`, `beforeEach`.
- Clearing collections between tests.

### Step‑by‑Step Tasks

1. **Configure test database URI**
   - In `.env.test` (create new file):

     ```env
     MONGODB_URI=mongodb://127.0.0.1:27017/task_app_test
     JWT_SECRET=test-secret
     ```

   - If you use MongoDB Atlas only, you can create a second DB name like `task_app_test` in the URI.

2. **Test setup file**
   - Create `__tests__/setup.js`:

     ```js
     require("dotenv").config({ path: ".env.test" });
     const mongoose = require("mongoose");
     const connectDB = require("../config/db");

     beforeAll(async () => {
       await connectDB(process.env.MONGODB_URI);
     });

     afterAll(async () => {
       await mongoose.connection.dropDatabase();
       await mongoose.connection.close();
     });

     beforeEach(async () => {
       // Clear collections between tests
       const collections = await mongoose.connection.db.collections();
       for (const collection of collections) {
         await collection.deleteMany({});
       }
     });
     ```

   - In `package.json` Jest config:

     ```json
     "jest": {
       "testEnvironment": "node",
       "setupFilesAfterEnv": ["<rootDir>/__tests__/setup.js"]
     }
     ```

3. **Integration test: auth + tasks**
   - Create `__tests__/tasks.integration.test.js`:

     ```js
     const request = require("supertest");
     const app = require("../server");
     const User = require("../models/User");
     const Task = require("../models/Task");

     async function registerAndLogin(email = "test@example.com", password = "secret123") {
       await request(app).post("/api/auth/register").send({ email, password });
       const res = await request(app).post("/api/auth/login").send({ email, password });
       return res.body.token;
     }

     describe("Task API (integration)", () => {
       it("allows an authenticated user to create and list tasks", async () => {
         const token = await registerAndLogin();

         const createRes = await request(app)
           .post("/api/tasks")
           .set("Authorization", `Bearer ${token}`)
           .send({ title: "Test Task", description: "Integration test" });

         expect(createRes.status).toBe(201);
         expect(createRes.body).toHaveProperty("_id");
         expect(createRes.body.title).toBe("Test Task");

         const listRes = await request(app)
           .get("/api/tasks")
           .set("Authorization", `Bearer ${token}`);

         expect(listRes.status).toBe(200);
         expect(listRes.body.length).toBe(1);
         expect(listRes.body[0].title).toBe("Test Task");
       });

       it("rejects unauthenticated access to tasks", async () => {
         const res = await request(app).get("/api/tasks");
         expect(res.status).toBe(401);
       });
     });
     ```

4. **Run tests**
   - Ensure MongoDB is running locally (if using local).
   - Run:

     ```bash
     npm test
     ```

   - Fix any issues (connection, paths, etc.). Read the stack traces carefully.

### Practice Exercises
- Add tests for:
  - Updating a task’s status.
  - Deleting a task.
  - Ensuring one user cannot see another user’s tasks (create tasks for two users and check separation).

### Reflection
In `notes-week11-day2.md`:

- What parts of the integration tests felt repetitive?
- How could helper functions (`registerAndLogin`) reduce duplication?

### Optional Stretch
- Look up `mongodb-memory-server` and note how you might use it instead of a real Mongo instance (you don’t have to implement it now).

---

## Day 3 – Frontend Testing Intro + Manual End‑to‑End Flows

### Goals
- Understand basics of **React Testing Library** (RTL) or at least the idea.
- Create a **manual end‑to‑end test checklist** for your entire app.
- Run through your app as a user and log issues.

### Concepts
- Testing pyramid (unit < integration < e2e).
- React Testing Library (optional, one test).
- Manual E2E: flows like “register + login + create task + logout”.

### Step‑by‑Step Tasks

1. **Install React Testing Library (optional but recommended)**  
   In your React project:

   ```bash
   npm install --save-dev @testing-library/react @testing-library/jest-dom
   ```

   - In `vite.config.js`, nothing extra needed; Vite template with Vitest uses a different stack, but you can use Jest style or adopt Vitest.  
   To avoid complexity, you can keep RTL as an optional stretch and focus on manual flows.

2. **(Optional) Very simple React component test**
   - Add `src/components/Greeting.jsx`:

     ```jsx
     function Greeting({ name }) {
       return <p>Hello, {name}!</p>;
     }
     export default Greeting;
     ```

   - Add `src/components/Greeting.test.jsx` (if your project uses Vitest instead of Jest, adapt; otherwise you can skip implementation and just read about it).  
   If this feels like too many moving parts, mark it as a “later” topic.

3. **Create an E2E manual test checklist**
   - In `notes-week11-day3.md`, write:

     ```text
     End-to-End Checklist v1:

     1. Registration
        - [ ] Go to /register (or register form)
        - [ ] Register new user with valid email + password
        - [ ] See success message or can log in immediately
        - [ ] Try registering same email again → get an error

     2. Login/Logout
        - [ ] Login with valid credentials
        - [ ] See dashboard with correct email
        - [ ] Refresh page → still logged in (token works)
        - [ ] Logout → redirected to login, no dashboard access

     3. Tasks CRUD
        - [ ] Create a task with title + description
        - [ ] Task appears in list with correct data
        - [ ] Change status (todo → in-progress → done)
        - [ ] Edit task title/description and see changes
        - [ ] Delete task and confirm it disappears

     4. Filters & Stats
        - [ ] Filter by status: see only that subset
        - [ ] Stats counts match visible tasks

     5. Multi-user isolation
        - [ ] User A creates tasks
        - [ ] User B logs in and sees only their own tasks

     6. Error handling
        - [ ] Turn off backend and see how frontend behaves (friendly error?)
        - [ ] Invalid login shows useful message
     ```

4. **Execute the checklist**
   - Open your app in browser.
   - Use local dev backend + frontend.
   - For each checkbox:
     - Perform the steps.
     - Note any bugs, weird behavior, or confusing UI.

5. **Create a bug list**
   - In the same notes file, note:
     - Short bug description.
     - Steps to reproduce.
     - Severity (low/medium/high).

### Practice Exercises
- Fix 1–2 small bugs or UX issues you found.
- Update the checklist after fixing to ensure it stays current.

### Reflection
Add to `notes-week11-day3.md`:

- Did manual testing reveal anything your code “looked fine” on?
- Which flow felt most brittle or complex?

### Optional Stretch
- Research Cypress or Playwright for automated E2E testing; jot down in your notes how you might test your login flow with such tools later.

---

## Day 4 – Refactoring & Code Organization

### Goals
- Improve code structure in backend and frontend.
- Remove dead code, duplication, and unclear naming.
- Prepare a clean base for deployment.

### Concepts
- Code smells: long functions, repeated logic, unclear names.
- Separation of concerns:
  - `routes/`, `models/`, `middleware/`, `config/`.
  - In React: `components/`, `hooks/`, `pages/`, `api/`, `ui/`.
- Small, focused components/hooks.

### Step‑by‑Step Tasks

1. **Backend structure review**
   - Open your backend project and review folders:
     - `models/` (User, Task, Note?)
     - `routes/` (authRoutes, taskRoutes, notesRoutes)
     - `middleware/` (auth, error, maybe logger)
     - `config/db.js`
     - `server.js`
   - In `notes-week11-day4.md`, answer:
     - Are any files too large or doing too many things?
     - Any obvious duplication (same validation, same error messages)?

2. **Extract common patterns where useful**
   Examples:

   - Repeated `auth` checks are already in `authMiddleware`, good.
   - If you reuse similar “ownership” checks, you might abstract a helper function (but don’t over‑generalize prematurely).
   - Shared validation messages: you might centralize in a small `validation.js` file.

3. **Make `server.js` minimal**
   - Aim for something like:

     ```js
     require("dotenv").config();
     const express = require("express");
     const cors = require("cors");
     const connectDB = require("./config/db");

     const authRoutes = require("./routes/authRoutes");
     const taskRoutes = require("./routes/taskRoutes");

     const app = express();

     app.use(cors({ origin: "http://localhost:5173" })); // later add prod origin
     app.use(express.json());

     app.use("/api/auth", authRoutes);
     app.use("/api/tasks", taskRoutes);

     // 404 and error handlers...

     if (require.main === module) {
       const PORT = process.env.PORT || 3000;
       (async () => {
         await connectDB(process.env.MONGODB_URI);
         app.listen(PORT, () => {
           console.log(`Server running on http://localhost:${PORT}`);
         });
       })();
     }

     module.exports = app;
     ```

   - Ensure 404 + error handlers are still present but not bloated.

4. **Frontend structure review**
   - Suggested layout:

     ```text
     src/
       api.js
       AuthContext.jsx
       App.jsx
       main.jsx
       pages/
         LoginPage.jsx
         RegisterPage.jsx
         TaskDashboardPage.jsx
       components/
         TaskList.jsx
         TaskItem.jsx
         TaskForm.jsx
       hooks/
         useTasks.js
       ui/
         Loading.jsx
         ErrorMessage.jsx
         EmptyState.jsx
     ```

   - Move files into these folders accordingly and update imports.

5. **Simplify complex components**
   - If `TaskDashboardPage.jsx` is > ~200 lines:
     - Extract `<TaskForm>`, `<TaskFilters>`, `<TaskStats>` components:
       - Each receives props; logic stays mostly in `TaskDashboardPage` and `useTasks`.

### Practice Exercises
- Rename at least 2–3 variables or functions to be clearer (e.g., `loadData` → `loadTasks`).
- Add comments only where logic is not obvious (avoid commenting every line).

### Reflection
In `notes-week11-day4.md`:

- After refactoring, which file feels easier to understand?
- Did you remove any code that you realized you no longer needed?

### Optional Stretch
- Add a small `logger` middleware that logs `[method] [url]` and status code, and make sure it’s only enabled in development (use `process.env.NODE_ENV`).

---

## Day 5 – Deploy Backend (Render/Railway) with Env Vars & CORS

### Goals
- Push backend code to GitHub (if not already).
- Deploy API to a cloud host (Render, Railway, or similar).
- Configure environment variables, CORS, and test live endpoints.

### Concepts
- Deployment basics:
  - Remote repo → build/start command → environment variables.
- Production vs development config.
- Updating `origin` in CORS for production frontend.

### Step‑by‑Step Tasks

1. **Prepare backend for deployment**
   - Ensure `package.json` has:

     ```json
     "scripts": {
       "start": "node server.js",
       "dev": "nodemon server.js",
       "test": "jest --runInBand"
     },
     "engines": {
       "node": ">=18"
     }
     ```

   - Add a simple health route if you haven’t:

     ```js
     app.get("/api/health", (req, res) => {
       res.json({ status: "ok" });
     });
     ```

2. **Push backend to GitHub**
   - If not already:

     ```bash
     git init
     git add .
     git commit -m "Backend: Task Manager API"
     git branch -M main
     git remote add origin https://github.com/your-username/task-manager-api.git
     git push -u origin main
     ```

3. **Create a deployment on Render (example)**
   - Go to render.com → create account.
   - “New” → “Web Service” → connect your GitHub repo.
   - Configure:
     - Build Command: `npm install`
     - Start Command: `npm start`
   - Environment:
     - `MONGODB_URI` (use your Atlas URI).
     - `JWT_SECRET`.
     - `NODE_ENV=production` (optional but recommended).
   - Deploy.

4. **Set correct port & binding**
   - Render uses `PORT` env var automatically, and your app reads `process.env.PORT || 3000`, so you’re fine.

5. **Test live API**
   - Once deployed, Render gives you a URL like `https://task-manager-api.onrender.com`.
   - Test with browser/Postman:
     - `GET https://.../api/health`
     - `POST https://.../api/auth/register`
   - If CORS errors occur, that will be fixed tomorrow when frontend is deployed; for now, just test with Postman or curl.

### Practice Exercises
- Add a `README.md` to your backend repo explaining:
  - What the API does.
  - Main endpoints (auth, tasks).
  - How to run locally.

### Reflection
In `notes-week11-day5.md`:

- Which part of deployment felt most confusing: repo setup, env vars, or service config?
- What’s your live backend URL?

### Optional Stretch
- Add a `/api/version` route that returns a version string and last deploy date (hard‑coded for now).

---

## Day 6 – Deploy Frontend (Vercel/Netlify) & Connect to Live API

### Goals
- Deploy your React app.
- Configure it to talk to the **live backend** instead of localhost.
- Adjust CORS on the backend to include your frontend’s domain.

### Concepts
- Build step (`npm run build`) and static hosting.
- Frontend environment variables with Vite:
  - `VITE_API_BASE_URL`.
- CORS for production origin.

### Step‑by‑Step Tasks

1. **Prepare React app for deployment**
   - In `src/api.js`, adjust:

     ```js
     const API_BASE =
       import.meta.env.VITE_API_BASE_URL || "http://localhost:3000";
     ```

   - This allows different base URLs for dev vs prod.

2. **Create `.env` for local dev (optional)**
   - In React project root `.env.local` (not committed):

     ```env
     VITE_API_BASE_URL=http://localhost:3000
     ```

3. **Push frontend to GitHub**
   - If not already:

     ```bash
     git init
     git add .
     git commit -m "Frontend: Task Manager React client"
     git branch -M main
     git remote add origin https://github.com/your-username/task-manager-client.git
     git push -u origin main
     ```

4. **Deploy with Vercel (example)**
   - Go to vercel.com → create/import project from your client repo.
   - Framework preset: React/Vite (or just Vite).
   - Set environment variable in Vercel:
     - `VITE_API_BASE_URL` = your live backend URL (e.g. `https://task-manager-api.onrender.com`).
   - Deploy.

5. **Update backend CORS for production**
   - In backend `server.js`:

     ```js
     const allowedOrigins = [
       "http://localhost:5173",
       "https://your-frontend-domain.vercel.app",
     ];

     app.use(
       cors({
         origin: (origin, callback) => {
           if (!origin || allowedOrigins.includes(origin)) {
             callback(null, true);
           } else {
             callback(new Error("Not allowed by CORS"));
           }
         },
         credentials: false,
       })
     );
     ```

   - Redeploy backend.

6. **Test live app end‑to‑end**
   - Open your Vercel URL:
     - Register.
     - Login.
     - Create/edit/delete tasks.
   - Watch Network tab:
     - Confirm requests go to your live API.
     - Confirm responses are 2xx, not CORS errors.

### Practice Exercises
- Test the same user in:
  - Local dev frontend + local backend.
  - Live frontend + live backend.
- Notice how configuration differs via `VITE_API_BASE_URL`.

### Reflection
In `notes-week11-day6.md`:

- What is your live frontend URL?
- Did you hit any CORS issues? How did you resolve them?

### Optional Stretch
- Add a link to your live Task Manager on your portfolio site from Week 2.

---

## Day 7 – Production Verification, Docs & Week Review

### Goals
- Perform a full **production** end‑to‑end test.
- Write basic **documentation** (README, architecture notes).
- Reflect on what you can now build and what to improve.

### Concepts
- Treating your app as a real product:
  - Clear instructions.
  - Known limitations / TODOs.
- Architecture overview: frontend, backend, DB, auth.

### Step‑by‑Step Tasks

1. **Full production test run**
   - Use your live URLs only.
   - Run your E2E checklist (from Day 3) **against production**:
     - Register, login, tasks CRUD, filters, multi‑user, error handling.
   - Note any differences between local and production behavior.

2. **Fix at least 1–2 production issues**
   - Examples:
     - Confusing message on error.
     - Slow initial load (maybe show clearer loading state).
     - A bug that only appears with slower network.

3. **Create/Update documentation (frontend + backend)**
   - Backend `README.md`:
     - Overview of stack (Node/Express, Mongo, JWT).
     - How to run locally:
       - Required env vars.
       - `npm install`, `npm run dev`, `npm test`.
     - Main endpoints:
       - `/api/auth/register`, `/api/auth/login`, `/api/tasks` (CRUD).
   - Frontend `README.md`:
     - Stack (React, Vite, React Router).
     - How to run locally:
       - `npm install`, `npm run dev`.
       - `VITE_API_BASE_URL` configuration.
     - Production URL.

4. **Architecture notes**
   - In `notes-week11-day7.md`, draw or describe briefly:
     - Browser (React) → `VITE_API_BASE_URL` → Express (Render) → MongoDB Atlas.
     - Where auth happens (JWT, `Authorization` header, `authMiddleware`).
     - Where tasks are filtered by user.

5. **Week 11 review**
   - In same notes file, answer:
     - 3 things you’re now comfortable with (e.g., “deploying Node/React apps”, “writing basic backend tests”).
     - 2 things that still feel fuzzy (e.g., “more advanced frontend tests”, “performance tuning”).
     - 1 improvement you’d make if you had more time (e.g., better UI, more tests, role‑based permissions).

### Practice Exercises
- Add a short “Known issues / Future work” section to your READMEs:
  - e.g., “No password reset yet”, “No pagination for tasks”, etc.

### Optional Stretch
- Set up a simple CI (GitHub Actions) that runs `npm test` on push for your backend repo.
- Timebox this to 1–2 hours max if you try it; if it’s too much right now, just note how you’d approach it later.

---

### After Week 11

You now have:

- A **tested** (at least at basic levels) backend with **auth + tasks**.
- A **deployed API** and **deployed React frontend**.
- An end‑to‑end understanding of:
  - Local dev → GitHub → Deployment → Production testing.
- Documentation that another developer (or future you) can follow.

Week 12 will focus on your **capstone**: picking a project (habit tracker, expense tracker, blog, etc.), planning it properly, and implementing it end‑to‑end using everything you’ve learned.
