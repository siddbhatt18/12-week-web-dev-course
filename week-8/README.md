### Week 8 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 8:**  
Add **user accounts and authentication** to your app:

- Users can **register** and **log in**
- Each request includes a **JWT** token
- Notes are **per user** and routes are **protected**

Assume ~1.5–3 hours/day. Use extra time on practice/optional sections.

I’ll assume you’re starting from your Week 6/7 Notes app:

- Express + MongoDB + Mongoose
- `Note` model with full CRUD
- Frontend with `api.js` + `main.js`

Create a new folder if you want to keep earlier versions, e.g. `week8-auth-notes`, by copying your Week 7 project.

---

## Day 1 – Auth Concepts & User Model + Registration

### Goals
- Understand **authentication vs authorization**.
- Create a `User` model.
- Build a **register** endpoint that stores a **hashed** password.

### Concepts to Learn
- **Authentication**: confirm who you are (login).
- **Authorization**: what you are allowed to access (your own notes vs others).
- Why we **never store plain passwords**.
- Hashing with `bcryptjs`:
  - `bcrypt.hash(plain, saltRounds)`
  - `bcrypt.compare(plain, hash)`

### Step‑by‑Step Tasks

1. **Install auth libs**
   - In your backend project folder:

     ```bash
     npm install bcryptjs jsonwebtoken
     ```

2. **Create `models/User.js`**
   - In `models/User.js`:

     ```js
     const mongoose = require("mongoose");

     const userSchema = new mongoose.Schema(
       {
         email: {
           type: String,
           required: [true, "Email is required"],
           unique: true,
           lowercase: true,
           trim: true,
         },
         passwordHash: {
           type: String,
           required: [true, "Password is required"],
         },
       },
       { timestamps: true }
     );

     const User = mongoose.model("User", userSchema);

     module.exports = User;
     ```

3. **Create auth routes file**
   - In `routes/authRoutes.js`:

     ```js
     const express = require("express");
     const bcrypt = require("bcryptjs");
     const User = require("../models/User");

     const router = express.Router();

     // POST /api/auth/register
     router.post("/register", async (req, res, next) => {
       try {
         const { email, password } = req.body;

         if (!email || !password) {
           return res.status(400).json({ error: "Email and password are required" });
         }

         if (password.length < 6) {
           return res.status(400).json({ error: "Password must be at least 6 characters" });
         }

         const existingUser = await User.findOne({ email });
         if (existingUser) {
           return res.status(409).json({ error: "Email already registered" });
         }

         const saltRounds = 10;
         const passwordHash = await bcrypt.hash(password, saltRounds);

         const user = await User.create({ email, passwordHash });

         // For now, we just confirm creation, no token yet
         res.status(201).json({
           id: user._id,
           email: user.email,
           createdAt: user.createdAt,
         });
       } catch (error) {
         next(error);
       }
     });

     module.exports = router;
     ```

4. **Wire auth routes in `server.js`**
   - In `server.js`:

     ```js
     const authRouter = require("./routes/authRoutes");
     app.use("/api/auth", authRouter);
     ```

5. **Add a JWT secret to `.env`**
   - In `.env`:

     ```env
     JWT_SECRET=some-long-random-string-here
     ```

   - Example: generate one quickly using an online UUID generator.  
     (You’ll actually use it tomorrow; you’re just setting it up now.)

6. **Test registration with Postman**
   - Endpoint: `POST http://localhost:3000/api/auth/register`
   - Body → JSON:

     ```json
     {
       "email": "test@example.com",
       "password": "secret123"
     }
     ```

   - Confirm:
     - 201 status.
     - Response body has `id` and `email`.
     - No plain password is returned.

### Practice Exercises
- Try registering with:
  - Missing password.
  - Password shorter than 6.
  - Same email twice.
- Observe and verify the status codes (400 vs 409) and error messages.

### Reflection & Self‑Check
Create `notes-week8-day1.md`:
- In your own words:
  - Why is hashing passwords essential?
  - What is the difference between storing `password` and `passwordHash`?

### Optional Stretch
- Add a simple email format check (basic regex or `includes("@")`) before creating a user and return 400 if it fails.

---

## Day 2 – Login & JWT-Based Authentication

### Goals
- Implement **login**.
- Verify passwords with `bcrypt.compare`.
- Generate a **JWT token** on successful login.

### Concepts to Learn
- JWT (JSON Web Token):
  - Encodes a payload (e.g., `{ userId }`) and is signed with a secret.
  - Sent in `Authorization: Bearer <token>` header.
- `jsonwebtoken`:
  - `jwt.sign(payload, secret, options)`
  - `jwt.verify(token, secret)`

### Step‑by‑Step Tasks

1. **Implement login route**
   - In `routes/authRoutes.js`:

     ```js
     const jwt = require("jsonwebtoken");

     // POST /api/auth/login
     router.post("/login", async (req, res, next) => {
       try {
         const { email, password } = req.body;

         if (!email || !password) {
           return res.status(400).json({ error: "Email and password are required" });
         }

         const user = await User.findOne({ email });
         if (!user) {
           return res.status(401).json({ error: "Invalid email or password" });
         }

         const passwordMatch = await bcrypt.compare(password, user.passwordHash);
         if (!passwordMatch) {
           return res.status(401).json({ error: "Invalid email or password" });
         }

         const token = jwt.sign(
           { userId: user._id },
           process.env.JWT_SECRET,
           { expiresIn: "7d" } // token valid for 7 days
         );

         res.json({
           token,
           user: {
             id: user._id,
             email: user.email,
           },
         });
       } catch (error) {
         next(error);
       }
     });
     ```

2. **Test login with Postman**
   - After registering a user (e.g., `test@example.com` / `secret123`):
     - `POST http://localhost:3000/api/auth/login`
     - JSON body:

       ```json
       {
         "email": "test@example.com",
         "password": "secret123"
       }
       ```

   - Confirm:
     - 200 status.
     - Response contains `token` (a long string with dots) and `user`.

   - Try wrong password:
     - Should get 401.

3. **Understand JWT structure (quick peek)**
   - Paste the JWT into https://jwt.io (just the token, not the secret).
   - Observe:
     - Header: alg, typ.
     - Payload: `userId`, `iat`, `exp`.
   - **Don’t** paste your secret there.

4. **Update error handler for 401 clarity (optional)**
   - In `server.js`, you may decide to:
     - Treat authentication errors with status 401.
     - Keep your `ValidationError` / `CastError` handling.

### Practice Exercises
- Add a tiny helper in `authRoutes.js` which standardizes the “Invalid email or password” message so you don’t leak which one was wrong.
- Try setting `expiresIn: "1m"` and watch what happens after a minute (later this week).

### Reflection & Self‑Check
In `notes-week8-day2.md`:
- What information does your JWT currently store?
- Why is it safe(‑ish) to store only a `userId` in the token, but not a password?

### Optional Stretch
- Add an endpoint `GET /api/auth/test` that just returns a message, and tomorrow you’ll protect it with auth middleware.

---

## Day 3 – Auth Middleware & Protecting Routes

### Goals
- Write an **authentication middleware** that:
  - Reads token from `Authorization` header.
  - Verifies it and attaches `req.user`.
- Protect your **notes routes** so only logged‑in users can access them.
- Tie notes to the user who created them.

### Concepts to Learn
- `Authorization: Bearer <token>` convention.
- Middleware pattern in Express:
  - `(req, res, next)` functions.
- Data ownership:
  - Each note has a `user` field referencing `User._id`.

### Step‑by‑Step Tasks

1. **Add `user` field to Note schema**
   - In `models/Note.js`:

     ```js
     const noteSchema = new mongoose.Schema(
       {
         title: { ... },
         content: { ... },
         user: {
           type: mongoose.Schema.Types.ObjectId,
           ref: "User",
           required: true,
         },
       },
       { timestamps: true }
     );
     ```

   - This means every note must belong to a user.

2. **Create auth middleware**
   - In `middleware/auth.js`:

     ```js
     const jwt = require("jsonwebtoken");
     const User = require("../models/User");

     async function authMiddleware(req, res, next) {
       try {
         const authHeader = req.headers.authorization || "";
         const [scheme, token] = authHeader.split(" ");

         if (scheme !== "Bearer" || !token) {
           return res.status(401).json({ error: "Authorization token missing or malformed" });
         }

         const decoded = jwt.verify(token, process.env.JWT_SECRET);
         const user = await User.findById(decoded.userId).select("-passwordHash");

         if (!user) {
           return res.status(401).json({ error: "User not found" });
         }

         req.user = user; // attach user to request
         next();
       } catch (error) {
         if (error.name === "JsonWebTokenError" || error.name === "TokenExpiredError") {
           return res.status(401).json({ error: "Invalid or expired token" });
         }
         next(error);
       }
     }

     module.exports = authMiddleware;
     ```

3. **Use auth middleware on notes routes**
   - In `routes/notesRoutes.js`:

     ```js
     const authMiddleware = require("../middleware/auth");
     const router = express.Router();

     // Apply to all note routes:
     router.use(authMiddleware);
     ```

   - Now every route in this router requires authentication.

4. **Update note creation to set `user`**
   - In POST `/` route:

     ```js
     const newNote = await Note.create({
       title,
       content,
       user: req.user._id,
     });
     ```

5. **Update GET all notes to filter by user**
   - In GET `/`:

     ```js
     const { search, sort } = req.query;
     const query = { user: req.user._id };

     if (search) {
       query.$or = [
         { title: { $regex: search, $options: "i" } },
         { content: { $regex: search, $options: "i" } },
       ];
     }

     const notes = await Note.find(query).sort(sortOption);
     ```

6. **Update GET /:id, PATCH, DELETE to ensure ownership**
   - For each route, when you query, include user constraint:

     ```js
     const note = await Note.findOne({ _id: id, user: req.user._id });
     ```

   - For PATCH/DELETE, use `findOneAndUpdate` / `findOneAndDelete` with `{ _id: id, user: req.user._id }`.

### Testing with Postman

1. **Login**, copy the token.
2. **Call protected route without token**:
   - Expect 401.
3. **Call with token**:
   - In Postman → Authorization tab → Bearer Token, paste token.
   - Or set header `Authorization: Bearer <token>`.
   - Confirm you can:
     - Create a note.
     - Get only your notes.
4. Register a second user and confirm the two users don’t see each other’s notes.

### Practice Exercises
- Add a `GET /api/auth/me` route:
  - Uses `authMiddleware`.
  - Returns the current user’s `id` and `email`.

### Reflection & Self‑Check
In `notes-week8-day3.md`:
- Describe the full flow when a logged‑in user creates a note (from token to DB).
- Why is it important to always filter notes by both `_id` **and** `user`?

### Optional Stretch
- In your `Note` model, add `user: { ref: "User" }` and try using `.populate("user", "email")` in a debug route to see user info along with notes (just for learning, not necessarily in production endpoints).

---

## Day 4 – Polishing Auth: `/me`, Consistent Errors, and Security Basics

### Goals
- Finalize core auth endpoints:
  - `/register`, `/login`, `/me`.
- Ensure consistent 401/403 responses.
- Learn a few basic security best practices.

### Concepts to Learn
- 401 Unauthorized vs 403 Forbidden:
  - 401: not authenticated / invalid token.
  - 403: authenticated but not allowed (e.g., trying to access another user’s resource).
- Principle of least privilege.
- Don’t leak too much info in error messages.

### Step‑by‑Step Tasks

1. **Implement `/api/auth/me` properly**
   - In `routes/authRoutes.js`:

     ```js
     const authMiddleware = require("../middleware/auth");

     router.get("/me", authMiddleware, (req, res) => {
       res.json({
         id: req.user._id,
         email: req.user.email,
         createdAt: req.user.createdAt,
       });
     });
     ```

2. **Standardize unauthorized vs forbidden (optional but good practice)**
   - In notes routes, you’re already filtering by user; if note isn’t found, you currently return 404.
   - That’s OK (hides whether resource exists).
   - Just be consistent: always return 404 for missing/unauthorized resources, and 401 only for missing/invalid tokens.

3. **Review error handler**
   - Confirm in `server.js`:

     ```js
     app.use((err, req, res, next) => {
       console.error(err);

       if (err.name === "ValidationError") {
         return res.status(400).json({ error: err.message });
       }

       if (err.name === "CastError" && err.kind === "ObjectId") {
         return res.status(400).json({ error: "Invalid ID format" });
       }

       // You might also check for custom error types here

       res.status(500).json({ error: "Internal server error" });
     });
     ```

4. **Test all auth flows with Postman**
   - Register → Login → call `/me`.
   - Try calling `/me` without token.
   - Try calling notes endpoints without token.

5. **Security basics (write & think)**
   - In `notes-week8-day4.md`, write your initial checklist:
     - Never store plaintext passwords.
     - Use HTTPS in production (note only).
     - Store JWT secret outside code (`.env`).
     - Return generic messages for login errors (“Invalid email or password”).

### Practice Exercises
- Change your login route to always return the same message for invalid credentials (don’t distinguish between “no user” and “wrong password”).
- Add a short comment in `authMiddleware` explaining each step (retrieving header, splitting, verifying, loading user).

### Reflection & Self‑Check
- In `notes-week8-day4.md`, answer:
  - When should you use 401 vs 404 vs 500?
  - Why is it a bad idea to include password or passwordHash in any response?

### Optional Stretch
- Add a `role` field to `User` schema (`"user"` vs `"admin"`) and think about how you could restrict certain routes to admins only (you’ll wire this later if you want).

---

## Day 5 – Frontend: Login & Registration UI + Storing Tokens

### Goals
- Build a simple **login/register UI**.
- Log in from the browser and store the **JWT token**.
- Pass the token in API calls from the frontend.

### Concepts to Learn
- Auth state on the frontend:
  - `currentUser`, `token`.
- Where to store token:
  - For this learning project: `localStorage` is OK.
  - In production, more nuance (httpOnly cookies vs localStorage, etc.).
- Attaching `Authorization` headers in `fetch`.

### Step‑by‑Step Tasks

1. **Add auth section to frontend**
   - In `public/index.html`, structure your app into sections:

     ```html
     <body>
       <main class="app">
         <section id="auth-section">
           <h1>Notes App</h1>
           <h2>Login</h2>
           <form id="login-form">
             <input id="login-email" type="email" placeholder="Email" />
             <input id="login-password" type="password" placeholder="Password" />
             <button type="submit">Login</button>
           </form>

           <h2>Register</h2>
           <form id="register-form">
             <input id="register-email" type="email" placeholder="Email" />
             <input id="register-password" type="password" placeholder="Password (min 6 chars)" />
             <button type="submit">Register</button>
           </form>
         </section>

         <section id="notes-section" style="display: none;">
           <div id="header-bar">
             <span id="user-info"></span>
             <button id="logout-btn">Logout</button>
           </div>

           <div id="status"></div>

           <form id="note-form">
             <input id="note-title" type="text" placeholder="Title" />
             <textarea id="note-content" placeholder="Content"></textarea>
             <button type="submit">Add Note</button>
           </form>

           <ul id="notes-list"></ul>
         </section>
       </main>

       <script type="module" src="main.js"></script>
     </body>
     ```

2. **Extend your API module with auth calls**
   - In `public/api.js`, add:

     ```js
     const API_BASE = "http://localhost:3000";

     let authToken = null;

     export function setAuthToken(token) {
       authToken = token;
     }

     function getAuthHeaders() {
       const headers = { "Content-Type": "application/json" };
       if (authToken) {
         headers.Authorization = `Bearer ${authToken}`;
       }
       return headers;
     }

     // existing handleResponse, fetchNotesApi, etc. adjust to use getAuthHeaders

     export async function loginApi({ email, password }) {
       const res = await fetch(`${API_BASE}/api/auth/login`, {
         method: "POST",
         headers: { "Content-Type": "application/json" },
         body: JSON.stringify({ email, password }),
       });
       return handleResponse(res);
     }

     export async function registerApi({ email, password }) {
       const res = await fetch(`${API_BASE}/api/auth/register`, {
         method: "POST",
         headers: { "Content-Type": "application/json" },
         body: JSON.stringify({ email, password }),
       });
       return handleResponse(res);
     }

     export async function fetchMeApi() {
       const res = await fetch(`${API_BASE}/api/auth/me`, {
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }

     // Update note APIs to use getAuthHeaders()
     export async function fetchNotesApi(searchText = "", sort = "newest") {
       const params = new URLSearchParams();
       if (searchText) params.set("search", searchText);
       if (sort) params.set("sort", sort);

       const url = params.toString()
         ? `${API_BASE}/api/notes?${params.toString()}`
         : `${API_BASE}/api/notes`;

       const res = await fetch(url, {
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }

     export async function createNoteApi({ title, content }) {
       const res = await fetch(`${API_BASE}/api/notes`, {
         method: "POST",
         headers: getAuthHeaders(),
         body: JSON.stringify({ title, content }),
       });
       return handleResponse(res);
     }

     // same pattern for deleteNoteApi, updateNoteApi...
     ```

3. **Manage auth state in `main.js`**
   - In `main.js`:

     ```js
     import {
       setAuthToken,
       loginApi,
       registerApi,
       fetchMeApi,
       fetchNotesApi,
       createNoteApi,
       deleteNoteApi,
       updateNoteApi,
     } from "./api.js";

     const authSection = document.querySelector("#auth-section");
     const notesSection = document.querySelector("#notes-section");
     const userInfoEl = document.querySelector("#user-info");
     const logoutBtn = document.querySelector("#logout-btn");

     const loginForm = document.querySelector("#login-form");
     const loginEmail = document.querySelector("#login-email");
     const loginPassword = document.querySelector("#login-password");

     const registerForm = document.querySelector("#register-form");
     const registerEmail = document.querySelector("#register-email");
     const registerPassword = document.querySelector("#register-password");

     const statusEl = document.querySelector("#status");
     // ... existing note form, list, etc.

     let currentUser = null;

     function setStatus(message, type = "") {
       statusEl.textContent = message || "";
       statusEl.className = "";
       if (type) statusEl.classList.add(`status-${type}`);
     }

     function showAuth() {
       authSection.style.display = "block";
       notesSection.style.display = "none";
     }

     function showNotes() {
       authSection.style.display = "none";
       notesSection.style.display = "block";
     }

     function saveTokenToStorage(token) {
       localStorage.setItem("notesToken", token);
     }

     function loadTokenFromStorage() {
       return localStorage.getItem("notesToken");
     }

     async function initializeApp() {
       const token = loadTokenFromStorage();
       if (token) {
         try {
           setAuthToken(token);
           const me = await fetchMeApi();
           currentUser = me;
           userInfoEl.textContent = `Logged in as ${me.email}`;
           showNotes();
           await fetchAndRenderNotes();
           return;
         } catch (error) {
           console.error("Stored token invalid:", error);
           localStorage.removeItem("notesToken");
           setAuthToken(null);
         }
       }
       showAuth();
     }

     loginForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       try {
         const email = loginEmail.value.trim();
         const password = loginPassword.value;
         if (!email || !password) return;

         setStatus("Logging in...", "loading");
         const { token, user } = await loginApi({ email, password });
         setAuthToken(token);
         saveTokenToStorage(token);
         currentUser = user;
         userInfoEl.textContent = `Logged in as ${user.email}`;
         loginEmail.value = "";
         loginPassword.value = "";
         showNotes();
         setStatus("Logged in.", "success");
         await fetchAndRenderNotes();
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });

     registerForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       try {
         const email = registerEmail.value.trim();
         const password = registerPassword.value;
         if (!email || !password) return;

         setStatus("Registering...", "loading");
         await registerApi({ email, password });
         setStatus("Registered. You can now log in.", "success");
         registerEmail.value = "";
         registerPassword.value = "";
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });

     logoutBtn.addEventListener("click", () => {
       setAuthToken(null);
       localStorage.removeItem("notesToken");
       currentUser = null;
       showAuth();
       setStatus("Logged out.", "success");
     });

     // Your existing fetchAndRenderNotes, renderNotes, note handlers…
     initializeApp();
     ```

4. **Test login & notes in browser**
   - Register via frontend.
   - Log in, confirm:
     - Notes section shows.
     - Fetching notes works (should start empty).
     - Creating notes works and they persist in DB, tied to your user.
   - Refresh the page; your token in `localStorage` should keep you logged in.

### Practice Exercises
- Try logging in with wrong password and verify the error message appears in your status area.
- Open DevTools → Application → Local Storage and inspect the token.  
  Then delete it and reload to see you’re logged out.

### Reflection & Self‑Check
In `notes-week8-day5.md`:
- What exactly do you store in localStorage?
- What steps happen from clicking “Login” to seeing your notes?

### Optional Stretch
- Show a short message “Welcome, [email]” when a user logs in for the first time in a session.
- Add some minimal password strength hints on the frontend (e.g., “use at least 6 characters”).

---

## Day 6 – Handling Unauthorized States Gracefully & Tiny UX Improvements

### Goals
- Make your app handle **expired/invalid tokens** smoothly.
- Centralize “log out on 401” behavior.
- Improve small UX details around auth.

### Concepts to Learn
- Central error handling on the frontend for **401 responses**.
- Automatic logout when token is invalid.
- UX: disabling buttons while requests are in progress.

### Step‑by‑Step Tasks

1. **Handle 401 in `handleResponse`**
   - In `api.js`:

     ```js
     // export a callback that main.js can set
     let onUnauthorized = null;

     export function setOnUnauthorized(handler) {
       onUnauthorized = handler;
     }

     async function handleResponse(res) {
       let data = null;
       try {
         if (res.status !== 204) {
           data = await res.json();
         }
       } catch (e) {}

       if (!res.ok) {
         const message = data?.error || `Request failed with status ${res.status}`;

         if (res.status === 401 && onUnauthorized) {
           onUnauthorized();
         }

         throw new Error(message);
       }

       return data;
     }
     ```

2. **Hook unauthorized handler in `main.js`**
   - In `main.js`, after imports:

     ```js
     import { setOnUnauthorized, /* ...other imports */ } from "./api.js";

     function handleUnauthorized() {
       setAuthToken(null);
       localStorage.removeItem("notesToken");
       currentUser = null;
       showAuth();
       setStatus("Session expired or unauthorized. Please log in again.", "error");
     }

     setOnUnauthorized(handleUnauthorized);
     ```

   - Now, any 401 from the server will auto‑logout and show a message.

3. **Simulate token expiry/invalidity**
   - In Postman or by editing `.env`, set `expiresIn: "10s"` in `jwt.sign` temporarily.
   - Login, wait 10–20 seconds, then try fetching notes.
   - Observe how app logs you out when token is rejected.

4. **Disable form buttons during submissions (UX)**
   - In `main.js`, for login submit handler:

     ```js
     loginForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       loginForm.querySelector("button[type='submit']").disabled = true;
       try {
         // ... existing logic ...
       } catch (error) {
         // ...
       } finally {
         loginForm.querySelector("button[type='submit']").disabled = false;
       }
     });
     ```

   - Repeat similar pattern for register and note forms.

### Practice Exercises
- Intentionally break your JWT secret (change in `.env`) and restart server; observe the behavior on the frontend.
- In `handleUnauthorized`, also clear any displayed notes and forms for extra safety.

### Reflection & Self‑Check
In `notes-week8-day6.md`:
- Why is it useful to centralize handling of 401 responses?
- What UX improvements did you find most noticeable?

### Optional Stretch
- Display a small “Last synced at [time]” timestamp after each successful notes fetch.
- Add a simple loading spinner icon instead of plain text during long calls.

---

## Day 7 – Review & Mini Auth-Backed Feature

### Goals
- Solidify your understanding of auth by adding one small auth‑backed feature.
- Review the complete auth flow end‑to‑end.
- Reflect on what’s solid and what needs revisiting.

### Concepts to Reinforce
- User → token → middleware → resource ownership.
- Frontend auth state management.
- Security basics around auth.

### Step‑by‑Step Tasks

1. **Written recap**
   - In `notes-week8-day7.md`, write (in your own words):
     - How a user registers and logs in.
     - How a request from the frontend is authenticated on the backend.
     - How notes are tied to a user in Mongo.

2. **Choose one small auth‑backed feature**
   Examples:

   - **User profile “My Notes Stats”**
     - Backend: new route `/api/notes/stats` that returns count of notes for `req.user`.
     - Frontend: small panel showing “You have X notes”.

   - **Change password** (simplified)
     - Backend: route `/api/auth/change-password` requires current password and new password.
     - Frontend: simple form in notes section.

   - **Mark note as private/public** (still only owner can edit/delete)
     - Backend: `isPublic` field; route to toggle.
     - Frontend: icon/button to show/hide public state.

3. **Implement chosen feature end‑to‑end**
   For example, “My Notes Stats”:

   - Backend:
     ```js
     // in notesRoutes.js
     router.get("/stats", async (req, res, next) => {
       try {
         const count = await Note.countDocuments({ user: req.user._id });
         res.json({ count });
       } catch (error) {
         next(error);
       }
     });
     ```

   - API module:
     ```js
     export async function fetchNotesStatsApi() {
       const res = await fetch(`${API_BASE}/api/notes/stats`, {
         headers: getAuthHeaders(),
       });
       return handleResponse(res);
     }
     ```

   - Frontend (`main.js`):
     - Add a small div near `user-info` to show stats.
     - Call `fetchNotesStatsApi()` after login/fetch notes and display.

4. **Test thoroughly**
   - Try with multiple users (via different browsers/incognito).
   - Ensure stats/feature reflects each user’s data correctly.

5. **End‑of‑week reflection**
   - In `notes-week8-day7.md`, also answer:
     - 3 things about auth you now feel comfortable with.
     - 2 things that still feel confusing.
     - 1 thing you’d like to improve in your auth system later (e.g., password reset, email verification, better security around tokens).

### Optional Stretch
- Sketch (just in notes) how you might:
  - Add “admin” role and admin‑only routes.
  - Add refresh tokens for longer sessions.
  - Switch from localStorage tokens to httpOnly cookies for better security.

---

### After Week 8

By the end of this week, you should be able to:

- Create and manage users in Mongo with **hashed passwords**.
- Implement **register, login, me** endpoints.
- Use **JWTs** for authentication and write an Express auth middleware.
- Tie resources (notes) to specific users and protect them.
- Build a frontend that:
  - Logs in users.
  - Stores and uses tokens.
  - Logs out gracefully on unauthorized responses.

You now have the foundation for full **multi‑user apps**. Next, you’ll expand this into your **Task Manager** project and add more advanced features (better UX, more complex data models, possibly React in the following weeks).
