### Week 7 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 7:**  
Make your full‑stack app feel “real”: **robust frontend–backend integration, good error handling, CORS, cleaner code structure, and UX polish** on your Mongo‑backed Notes app.

Assume ~1.5–3 hours/day. Use extra time on practice/optional parts.

I’ll assume you’re starting from your Week 6 project (`week6-notes-mongo`) with:

- Express + MongoDB + Mongoose
- `Note` model
- `/api/notes` CRUD routes
- A simple frontend (`public/index.html`, `public/app.js`) that can list/add/delete notes

You can either keep the same folder or duplicate it to `week7-notes-integration` to preserve the earlier version.

---

## Day 1 – Stronger HTTP & Fetch Error Handling

### Goals
- Deepen your understanding of HTTP responses.
- Make your frontend show clear loading and error states.
- Avoid silent failures by handling `res.ok` and `res.status` properly.

### Concepts to Learn
- HTTP status ranges:
  - 2xx: success (200, 201, 204)
  - 4xx: client errors (400, 404)
  - 5xx: server errors (500)
- `fetch`:
  - `res.ok`, `res.status`, `res.json()`
- Distinguish **network errors** vs **application errors**.

### Step‑by‑Step Tasks

1. **Add a “status” area to your frontend**
   - In `public/index.html`, inside `.notes-app` (or main), add:

     ```html
     <div id="status"></div>
     ```

   - In `public/styles.css`, style it:

     ```css
     #status {
       min-height: 20px;
       margin-bottom: 8px;
       font-size: 0.9rem;
     }

     .status-loading {
       color: #2980b9;
     }

     .status-error {
       color: #e74c3c;
     }

     .status-success {
       color: #27ae60;
     }
     ```

2. **Utility to update status text**
   - In `public/app.js`:

     ```js
     const statusEl = document.querySelector("#status");

     function setStatus(message, type = "") {
       statusEl.textContent = message || "";
       statusEl.className = ""; // reset classes
       if (type) {
         statusEl.classList.add(`status-${type}`);
       }
     }
     ```

3. **Create a helper to handle fetch responses**
   - Still in `app.js`:

     ```js
     async function handleResponse(res) {
       let data = null;
       try {
         // Try to parse JSON if any body
         if (res.status !== 204) {
           data = await res.json();
         }
       } catch (e) {
         // no JSON, that’s fine
       }

       if (!res.ok) {
         const errorMessage = data?.error || `Request failed with status ${res.status}`;
         throw new Error(errorMessage);
       }

       return data;
     }
     ```

4. **Update `fetchNotes` to use status + error handling**
   - Rewrite `fetchNotes`:

     ```js
     async function fetchNotes() {
       setStatus("Loading notes...", "loading");
       notesList.innerHTML = "<li>Loading...</li>";

       try {
         const res = await fetch("/api/notes");
         const notes = await handleResponse(res);
         renderNotes(notes);
         setStatus("Notes loaded.", "success");
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
         notesList.innerHTML = "<li>Error loading notes.</li>";
       }
     }
     ```

5. **Update your “add note” submit handler similarly**
   - In `noteForm.addEventListener("submit", ...)`:

     ```js
     noteForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       const title = titleInput.value.trim();
       const content = contentInput.value.trim();
       if (!title || !content) {
         setStatus("Title and content are required.", "error");
         return;
       }

       try {
         setStatus("Saving note...", "loading");
         const res = await fetch("/api/notes", {
           method: "POST",
           headers: { "Content-Type": "application/json" },
           body: JSON.stringify({ title, content }),
         });
         await handleResponse(res);
         titleInput.value = "";
         contentInput.value = "";
         setStatus("Note saved.", "success");
         fetchNotes();
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });
     ```

6. **Simulate a server error**
   - In `routes/notesRoutes.js`, temporarily add a “broken” route:

     ```js
     router.get("/debug/error", (req, res, next) => {
       next(new Error("Intentional test error"));
     });
     ```

   - Hit `/api/notes/debug/error` in the browser and see how your frontend reacts if you call it manually with `fetch` from the console.

### Practice Exercises
- Temporarily break your MongoDB connection string or shut down Mongo:
  - Restart server and try loading notes.
  - Observe your status messages and console logs.
- Modify `setStatus` so that success messages automatically clear after a few seconds (optional).

### Reflection & Self‑Check
Create `notes-week7-day1.md`:
- Explain the difference between:
  - A network error (e.g., server offline)  
  - A 400/500 HTTP error (server is running, but request/processing failed)
- Write down your new standard pattern for `fetch` + `handleResponse`.

### Optional Stretch
- Add a small “Retry” button that appears when there’s an error and calls `fetchNotes()` again.

---

## Day 2 – CORS & Decoupling Frontend from Backend

### Goals
- Understand **same-origin policy** and **CORS**.
- Run your API and your frontend from **different origins**.
- Configure Express with `cors` so your API can be used by other frontends.

### Concepts to Learn
- Origin = scheme + host + port (e.g., `http://localhost:3000`).
- Same‑origin vs cross‑origin requests.
- `Access-Control-Allow-Origin` header.
- `cors` middleware in Express.

### Step‑by‑Step Tasks

1. **Install CORS middleware**
   - In your backend project folder:

     ```bash
     npm install cors
     ```

2. **Enable CORS in `server.js`**
   - At the top:

     ```js
     const cors = require("cors");
     ```

   - After `app.use(express.json());`:

     ```js
     app.use(cors({
       origin: "http://localhost:5500", // we'll serve frontend from here
     }));
     ```

   - For learning, you can temporarily use `origin: "*"`, but specifying a real origin is better practice.

3. **Create a separate frontend folder**
   - At same level as your backend project (or inside it, but outside `public`), create:
     - `week7-notes-frontend/`
       - `index.html`
       - `app.js`
   - Copy your existing `public/index.html` and `public/app.js` into this folder and adjust:
     - In `index.html`, include `<script src="app.js"></script>` at the end.
     - In `app.js`, change fetch URLs to absolute:

       ```js
       const API_BASE = "http://localhost:3000";

       // e.g.
       const res = await fetch(`${API_BASE}/api/notes`);
       // and
       const res = await fetch(`${API_BASE}/api/notes`, { ... });
       // etc.
       ```

4. **Serve the frontend from a different origin**
   - Easiest: use `live-server` or `npx serve`:

     ```bash
     cd week7-notes-frontend
     npx live-server --port=5500
     ```

     or

     ```bash
     npx serve -l 5500
     ```

   - Visit `http://localhost:5500/` to see your frontend.

5. **Test and observe CORS**
   - With `cors` enabled on the backend (`origin: "http://localhost:5500"`), your app should work.
   - Temporarily comment out `app.use(cors(...))` and restart server.
   - Try using the frontend again; you should see a CORS error in the browser console.

### Practice Exercises
- Change the allowed origin to something else (e.g., `http://localhost:1234`) and verify that your real frontend is now blocked again.
- Set `origin: "*"`, confirm it works, and then think/write about why this is not ideal in production.

### Reflection & Self‑Check
In `notes-week7-day2.md`:
- What is an “origin” in web security terms?
- In your own words, what problem does CORS solve and why does the browser enforce it?

### Optional Stretch
- Try serving your original Express‑hosted frontend (`public/`) and your new “decoupled” frontend side by side, and note that:
  - `http://localhost:3000/` → same origin as API.
  - `http://localhost:5500/` → cross-origin to API.

---

## Day 3 – Frontend Modularization: API Layer vs UI Layer

### Goals
- Clean up your frontend code by **separating API calls** from DOM logic.
- Use ES modules (`import` / `export`) in the browser.
- Make code easier to maintain and reuse later.

### Concepts to Learn
- ES modules in the browser:
  - `<script type="module">`
  - Named exports/imports.
- API client pattern: one file for all API calls.

### Step‑by‑Step Tasks

(You can do this either in the Express‑served `public/` frontend or in the separate `week7-notes-frontend`; I’ll assume Express `public/` for simplicity here, but the logic is identical.)

1. **Switch to modules**
   - In `public/index.html`, change the script tag to:

     ```html
     <script type="module" src="main.js"></script>
     ```

   - Rename `public/app.js` to:
     - `public/main.js` (entry point)
     - And you’ll create a new `public/api.js`.

2. **Create `public/api.js`**
   - This will hold all note‑related API calls:

     ```js
     const API_BASE = "http://localhost:3000"; // or relative: ""

     async function handleResponse(res) {
       let data = null;
       try {
         if (res.status !== 204) {
           data = await res.json();
         }
       } catch (e) {}
       if (!res.ok) {
         const message = data?.error || `Request failed with status ${res.status}`;
         throw new Error(message);
       }
       return data;
     }

     export async function fetchNotesApi(searchText = "") {
       const url = searchText
         ? `${API_BASE}/api/notes?search=${encodeURIComponent(searchText)}`
         : `${API_BASE}/api/notes`;

       const res = await fetch(url);
       return handleResponse(res);
     }

     export async function createNoteApi({ title, content }) {
       const res = await fetch(`${API_BASE}/api/notes`, {
         method: "POST",
         headers: { "Content-Type": "application/json" },
         body: JSON.stringify({ title, content }),
       });
       return handleResponse(res);
     }

     export async function deleteNoteApi(id) {
       const res = await fetch(`${API_BASE}/api/notes/${id}`, {
         method: "DELETE",
       });
       return handleResponse(res); // will return null for 204
     }

     export async function updateNoteApi(id, updates) {
       const res = await fetch(`${API_BASE}/api/notes/${id}`, {
         method: "PATCH",
         headers: { "Content-Type": "application/json" },
         body: JSON.stringify(updates),
       });
       return handleResponse(res);
     }
     ```

3. **Use the API module in `public/main.js`**
   - In `main.js`:

     ```js
     import {
       fetchNotesApi,
       createNoteApi,
       deleteNoteApi,
       updateNoteApi,
     } from "./api.js";

     const notesList = document.querySelector("#notes-list");
     const noteForm = document.querySelector("#note-form");
     const titleInput = document.querySelector("#note-title");
     const contentInput = document.querySelector("#note-content");
     const statusEl = document.querySelector("#status");

     function setStatus(message, type = "") {
       statusEl.textContent = message || "";
       statusEl.className = "";
       if (type) {
         statusEl.classList.add(`status-${type}`);
       }
     }

     async function fetchAndRenderNotes(searchText = "") {
       setStatus("Loading notes...", "loading");
       notesList.innerHTML = "<li>Loading...</li>";
       try {
         const notes = await fetchNotesApi(searchText);
         renderNotes(notes);
         setStatus("Notes loaded.", "success");
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
         notesList.innerHTML = "<li>Error loading notes.</li>";
       }
     }

     function renderNotes(notes) {
       notesList.innerHTML = "";
       if (notes.length === 0) {
         notesList.innerHTML = "<li>No notes yet. Add one!</li>";
         return;
       }

       notes.forEach((note) => {
         const li = document.createElement("li");
         li.className = "note-item";
         li.dataset.id = note._id;

         const infoDiv = document.createElement("div");
         const titleEl = document.createElement("div");
         titleEl.className = "note-title";
         titleEl.textContent = note.title;

         const contentEl = document.createElement("div");
         contentEl.textContent = note.content;

         infoDiv.appendChild(titleEl);
         infoDiv.appendChild(contentEl);

         const deleteBtn = document.createElement("button");
         deleteBtn.textContent = "Delete";
         deleteBtn.className = "delete-btn";

         li.appendChild(infoDiv);
         li.appendChild(deleteBtn);
         notesList.appendChild(li);
       });
     }

     noteForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       const title = titleInput.value.trim();
       const content = contentInput.value.trim();
       if (!title || !content) {
         setStatus("Title and content are required.", "error");
         return;
       }
       try {
         setStatus("Saving note...", "loading");
         await createNoteApi({ title, content });
         titleInput.value = "";
         contentInput.value = "";
         setStatus("Note saved.", "success");
         fetchAndRenderNotes();
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });

     notesList.addEventListener("click", async (event) => {
       const target = event.target;
       if (!target.classList.contains("delete-btn")) return;
       const li = target.closest(".note-item");
       const id = li.dataset.id;

       if (!confirm("Delete this note?")) return;

       try {
         setStatus("Deleting note...", "loading");
         await deleteNoteApi(id);
         setStatus("Note deleted.", "success");
         fetchAndRenderNotes();
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });

     fetchAndRenderNotes();
     ```

4. **Test and fix any module path issues**
   - Ensure you’re serving via HTTP (e.g., via Express or `live-server`), not directly opening the HTML file.

### Practice Exercises
- Add an exported function in `api.js` for searching (if you haven’t already) and call it from a search bar in `main.js`.
- Notice how much smaller `main.js` is compared to your earlier monolithic script.

### Reflection & Self‑Check
In `notes-week7-day3.md`:
- What are the benefits of having an `api.js` file?
- How would this organization help if you added other resources (e.g., tasks) later?

### Optional Stretch
- Create a separate `ui.js` module that exports `renderNotes`, `setStatus`, etc., and import it into `main.js`.  
  This further separates “talking to API” from “updating DOM”.

---

## Day 4 – Editing Notes from the Frontend

### Goals
- Implement **update (PATCH)** from the frontend.
- Design a simple, usable edit flow.
- Practice wiring UI actions → API calls → re-render.

### Concepts to Learn
- Designing UX for editing:
  - Inline forms vs modal vs reusing main form.
- PATCH from frontend with `updateNoteApi`.
- Keeping UI and data in sync.

### Step‑by‑Step Tasks

1. **Add an “Edit” button to each note**
   - In `renderNotes` (in `main.js`), modify to include Edit:

     ```js
     const editBtn = document.createElement("button");
     editBtn.textContent = "Edit";
     editBtn.className = "edit-btn";

     // in layout: [infoDiv] [Edit] [Delete]
     const actionsDiv = document.createElement("div");
     actionsDiv.appendChild(editBtn);
     actionsDiv.appendChild(deleteBtn);

     li.appendChild(infoDiv);
     li.appendChild(actionsDiv);
     ```

2. **Choose an edit flow**
   Two simple options:
   - **Inline edit**: replace title/content with `<input>`/`<textarea>` in the same list item.
   - **Reuse main form**: click “Edit” fills main form, plus store “currently editing id”.

   Use main form (simpler):

3. **Support “editing mode” in main form**
   - In `main.js`, add at top:

     ```js
     let editingNoteId = null;
     ```

   - Change form submit logic:

     ```js
     noteForm.addEventListener("submit", async (event) => {
       event.preventDefault();
       const title = titleInput.value.trim();
       const content = contentInput.value.trim();
       if (!title || !content) {
         setStatus("Title and content are required.", "error");
         return;
       }

       try {
         setStatus(editingNoteId ? "Updating note..." : "Saving note...", "loading");
         if (editingNoteId) {
           await updateNoteApi(editingNoteId, { title, content });
         } else {
           await createNoteApi({ title, content });
         }

         titleInput.value = "";
         contentInput.value = "";
         editingNoteId = null;
         noteForm.querySelector("button[type='submit']").textContent = "Add Note";
         setStatus("Note saved.", "success");
         fetchAndRenderNotes();
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
       }
     });
     ```

4. **Handle clicking “Edit”**
   - In `notesList.addEventListener("click", ...)`, extend to:

     ```js
     notesList.addEventListener("click", async (event) => {
       const target = event.target;
       const li = target.closest(".note-item");
       if (!li) return;
       const id = li.dataset.id;

       if (target.classList.contains("delete-btn")) {
         // existing delete logic
       } else if (target.classList.contains("edit-btn")) {
         // fill form with current title/content
         const titleEl = li.querySelector(".note-title");
         const contentEl = li.querySelector(".note-title + div"); // next div
         titleInput.value = titleEl.textContent;
         contentInput.value = contentEl.textContent;
         editingNoteId = id;
         noteForm.querySelector("button[type='submit']").textContent = "Update Note";
         setStatus("Editing note...", "loading");
       }
     });
     ```

5. **Test the full edit flow**
   - Create a note, click Edit, modify title/content, submit.
   - Confirm DB is updated (via API or Atlas).

### Practice Exercises
- Add a “Cancel Edit” button near the form that:
  - Clears inputs.
  - Resets `editingNoteId = null`.
  - Sets button text back to “Add Note”.

### Reflection & Self‑Check
In `notes-week7-day4.md`:
- Describe the steps (in words) from clicking “Edit” to seeing the updated note in the list.
- How did you ensure you’re editing the correct note?

### Optional Stretch
- Instead of reusing the main form, try implementing **inline editing**:
  - When clicking Edit, swap text for inputs inside the list item itself.
  - Add “Save” and “Cancel” buttons inline.

---

## Day 5 – Filters, Sorting & Remembering User Preferences

### Goals
- Improve UX with simple filters/sorting (e.g., by newest/oldest).
- Decide what should be done server‑side vs client‑side.
- Store a simple UI preference in `localStorage`.

### Concepts to Learn
- Client‑side vs server‑side filtering/sorting.
- Query parameters vs front‑end logic.
- `localStorage` to remember user choices.

### Step‑by‑Step Tasks

1. **Add sort controls to the UI**
   - In `index.html`, above the notes list:

     ```html
     <div id="controls">
       <label>
         Sort:
         <select id="sort-select">
           <option value="newest">Newest first</option>
           <option value="oldest">Oldest first</option>
         </select>
       </label>
     </div>
     ```

2. **Extend your API to support sort query (server‑side)**
   - In `routes/notesRoutes.js` GET `/` route, modify:

     ```js
     router.get("/", async (req, res, next) => {
       try {
         const { search, sort } = req.query;
         const query = {};

         if (search) {
           query.$or = [
             { title: { $regex: search, $options: "i" } },
             { content: { $regex: search, $options: "i" } },
           ];
         }

         let sortOption = { createdAt: -1 }; // default newest
         if (sort === "oldest") {
           sortOption = { createdAt: 1 };
         }

         const notes = await Note.find(query).sort(sortOption);
         res.json(notes);
       } catch (error) {
         next(error);
       }
     });
     ```

3. **Update `fetchNotesApi` to accept sort**
   - In `api.js`:

     ```js
     export async function fetchNotesApi(searchText = "", sort = "newest") {
       const params = new URLSearchParams();
       if (searchText) params.set("search", searchText);
       if (sort) params.set("sort", sort);

       const url = params.toString()
         ? `${API_BASE}/api/notes?${params.toString()}`
         : `${API_BASE}/api/notes`;

       const res = await fetch(url);
       return handleResponse(res);
     }
     ```

4. **Wire sort select in frontend & remember preference**
   - In `main.js`:

     ```js
     const sortSelect = document.querySelector("#sort-select");

     let currentSearch = "";
     let currentSort = localStorage.getItem("notesSort") || "newest";

     sortSelect.value = currentSort;

     sortSelect.addEventListener("change", () => {
       currentSort = sortSelect.value;
       localStorage.setItem("notesSort", currentSort);
       fetchAndRenderNotes(currentSearch, currentSort);
     });

     async function fetchAndRenderNotes(searchText = "", sort = currentSort) {
       currentSearch = searchText;
       setStatus("Loading notes...", "loading");
       notesList.innerHTML = "<li>Loading...</li>";
       try {
         const notes = await fetchNotesApi(searchText, sort);
         renderNotes(notes);
         setStatus("Notes loaded.", "success");
       } catch (error) {
         console.error(error);
         setStatus(error.message, "error");
         notesList.innerHTML = "<li>Error loading notes.</li>";
       }
     }

     // Ensure your search (if you implemented it earlier) also uses fetchAndRenderNotes with the sort.
     ```

5. **Test**
   - Change sort order and refresh page:
     - `localStorage` should keep your last choice.

### Practice Exercises
- Add a simple “Show only notes containing: [text]” input that uses client‑side filtering:
  - Fetch all notes once, then filter in JS based on this text and re‑render.

### Reflection & Self‑Check
In `notes-week7-day5.md`:
- What trade‑offs did you notice between server‑side and client‑side filtering/sorting?
- What kind of preference data is appropriate to store in `localStorage`?

### Optional Stretch
- Add a toggle “Group by title first letter” (client‑side only).
- Think about performance: when would server‑side filtering/sorting be absolutely necessary?

---

## Day 6 – Debugging Fire Drills & Code Review

### Goals
- Practice systematic debugging across frontend and backend.
- Do a light code review of your own project.
- Build confidence in reading stack traces and network logs.

### Concepts to Reinforce
- Debugging steps:
  - Reproduce → Isolate → Inspect logs → Form hypothesis → Test fix.
- Using Browser DevTools Network tab:
  - Status, response body, headers.
- Reading Node/Express error logs and stack traces.

### Step‑by‑Step Tasks

1. **Intentionally break things and fix them**
   - Scenario A: Wrong endpoint URL in client
     - In `api.js`, temporarily change one URL to `/api/notess`.
     - Use the app and observe:
       - Network tab: 404.
       - Console: error message you throw.
     - Fix and confirm.

   - Scenario B: Throw an error in a route
     - In `notesRoutes.js` GET `/`, temporarily add `throw new Error("Test crash");`.
     - Hit the page, observe 500 error, UI status, backend console.
     - Remove the throw and re‑test.

   - Scenario C: Database offline
     - Stop MongoDB or change the URI in `.env` to something invalid.
     - Restart server and watch it fail or log error.
     - Restore and restart.

2. **Code review pass**
   - Skim `server.js`, `config/db.js`, `models/Note.js`, `routes/notesRoutes.js`, `public/api.js`, `public/main.js`.
   - Look for:
     - Very long functions: can anything be split?
     - Duplicate logic: can anything be re‑used?
     - Comment any non‑obvious parts.

   - Rename at least one poorly named variable or function to something clearer.

3. **Create a manual test checklist**
   - In `notes-week7-day6.md`, create a checklist like:

     - [ ] Load notes list
     - [ ] Add new note
     - [ ] Edit existing note
     - [ ] Delete note
     - [ ] Search notes
     - [ ] Change sort order
     - [ ] Error when leaving title empty
     - [ ] Error when server is down (shows friendly message)

   - Run through it in the browser and mark pass/fail.

### Practice Exercises
- Add `console.log` in one backend route to log `req.method`, `req.url`, and maybe `req.body`.  
  See how this helps you reason about incoming traffic.

### Reflection & Self‑Check
Add to `notes-week7-day6.md`:
- When you see an error in the browser, what’s your sequence of places to check (browser console, Network tab, server logs, etc.)?
- What was the most surprising bug you saw today?

### Optional Stretch
- Try using `debugger;` in your frontend code and stepping through it in DevTools Sources tab.
- Similarly, use `node --inspect` (or VS Code debugger) to step through a backend route if you feel up to it.

---

## Day 7 – Mini “Notes Dashboard” & Week Review

### Goals
- Bring everything together into a slightly more “app‑like” experience.
- Add one small feature of your choice.
- Reflect on your end‑to‑end full‑stack understanding before moving to auth.

### Concepts to Reinforce
- Designing small features end‑to‑end:
  - DB schema (if needed)
  - API change
  - Frontend update
- Thinking in terms of user stories.

### Step‑by‑Step Tasks

1. **Pick one small, meaningful feature**
   Examples (choose ONE to fully implement):

   - **Favorite/Star notes**
     - Add `favorite: Boolean` to Note schema (default false).
     - Backend: PATCH endpoint to toggle favorite.
     - Frontend: Star icon/button; filter “Show favorites only”.

   - **Note details view**
     - Backend: Already have GET `/api/notes/:id`.
     - Frontend: Click a note to open a “details” panel (show createdAt, etc.).
   
   - **Soft delete / Trash**
     - Add `deleted: Boolean` instead of deleting from DB.
     - Backend: adjust GET to ignore deleted unless `?includeDeleted=true`.
     - Frontend: “Trash” view for deleted notes.

2. **Implement the feature end‑to‑end**
   - Plan first, write a short spec in `notes-week7-day7.md`:
     - What does user do?
     - What should happen in DB?
     - What should UI show?

   - Then:
     - Update `Note` schema if needed.
     - Update or add routes (e.g., `/api/notes/:id/favorite` or use PATCH).
     - Update frontend API module (`api.js`).
     - Update UI and event handlers (`main.js`).

3. **Test your feature thoroughly**
   - Use Postman to test new/changed endpoints.
   - Use browser to test frontend flow.
   - Add your new cases to the manual test checklist from Day 6.

4. **Week 7 review**
   - In `notes-week7-day7.md`, write:
     - 3 things you now understand much better than a week ago.
     - 2 things that still feel a bit fuzzy.
     - 1 thing you’re excited to learn next (likely: user accounts & auth).

### Practice Exercises
- If your feature introduced new query params or state, update your `fetchNotesApi` and UI to handle them gracefully.

### Optional Stretch
- Do a very short “demo write‑up” for your Notes app as if it were a portfolio project:
  - What tech it uses (Express, Mongo, Mongoose, vanilla JS).
  - What features it supports (CRUD, search, sort, edit, etc.).
  - A few screenshots (you can take them manually) and keep them in your notes.

---

### After Week 7

By the end of this week, you should:

- Feel comfortable wiring frontend and backend together with `fetch`.
- Understand CORS and be able to run frontend and backend from different origins.
- Have a **cleaner code structure** with an API module and clearer UI logic.
- Be able to add small features end‑to‑end: DB → API → UI.
- Have stronger debugging habits across the whole stack.

You’re now ready to add **users and authentication** (Week 8), turning your notes/task apps into multi‑user systems with login and protected data.
