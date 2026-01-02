### Week 6 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 6:**  
Move your API from in‑memory data to a **real database (MongoDB)** using **Mongoose**. By the end of the week you’ll have a **persistent Notes API + simple frontend client**.

Assume ~1.5–3 hours/day. Use extra time on practice/optional sections.

Recommended folder:

- `week6-notes-mongo/`
  - `server.js`
  - `models/Note.js`
  - `routes/notesRoutes.js`
  - `public/` (simple frontend)
  - `.env` (for DB connection string, later)

You’ll build on your Week 5 notes API. If you still have that code, you can copy it into this new folder as a starting point.

---

## Day 1 – MongoDB & Mongoose Basics + Connecting

### Goals
- Understand what MongoDB is and why we’re using it.
- Set up a MongoDB database (use MongoDB Atlas for simplicity).
- Connect to Mongo from Node using Mongoose.

### Concepts to Learn
- MongoDB:
  - NoSQL document database.
  - Database → collections → documents (JSON‑like).
- Mongoose:
  - ODM (Object Data Modeling) library for Mongo.
  - Connect, define schemas & models.

### Step‑by‑Step Tasks

1. **Create project folder & copy server code**
   - Make a folder: `week6-notes-mongo`.
   - Copy your Week 5 `server.js` and `notesRoutes.js` into it.
   - Run `npm init -y` if package.json doesn’t exist.
   - Install dependencies:

     ```bash
     npm install express mongoose
     npm install --save-dev nodemon
     ```

   - In `package.json` add scripts if missing:

     ```json
     "scripts": {
       "dev": "nodemon server.js",
       "start": "node server.js"
     }
     ```

2. **Create a free MongoDB Atlas cluster**
   - Go to https://www.mongodb.com/atlas and create a free account (if you don’t have one).
   - Create a free shared cluster.
   - Create:
     - A database user (username/password).
     - A database name (e.g., `notes_app`).
   - Add your IP address to the access list (or choose “Allow access from anywhere” for learning).
   - Get the **connection string**, which looks like:

     ```
     mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
     ```

   - You’ll adjust it later to include your DB name.

3. **Install dotenv for environment variables**
   - In terminal:

     ```bash
     npm install dotenv
     ```

   - Create a `.env` file in project root:

     ```env
     MONGODB_URI=mongodb+srv://USERNAME:PASSWORD@cluster0.xxxxx.mongodb.net/notes_app?retryWrites=true&w=majority
     PORT=3000
     ```

     Replace USERNAME, PASSWORD, and cluster URL parts with your real details.

   - Add `.env` to `.gitignore` (so you don’t commit secrets):

     ```txt
     node_modules
     .env
     ```

4. **Connect with Mongoose (in `server.js`)**
   - At the top of `server.js`:

     ```js
     require("dotenv").config();
     const express = require("express");
     const mongoose = require("mongoose");

     const app = express();
     const PORT = process.env.PORT || 3000;

     app.use(express.json());
     ```

   - Add the MongoDB connection before your routes:

     ```js
     async function connectDB() {
       try {
         await mongoose.connect(process.env.MONGODB_URI);
         console.log("Connected to MongoDB");
       } catch (error) {
         console.error("Error connecting to MongoDB:", error.message);
         process.exit(1); // stop app if DB connection fails
       }
     }

     connectDB();
     ```

   - Keep your existing routes and `app.listen` at the bottom:

     ```js
     app.listen(PORT, () => {
       console.log(`Server running on http://localhost:${PORT}`);
     });
     ```

5. **Run the server**
   - In terminal:

     ```bash
     npm run dev
     ```

   - If successful, you should see “Connected to MongoDB” plus your server running.
   - If not, carefully read the error message; most common issues:
     - Connection string typo.
     - Wrong username/password.
     - IP not allowed in Atlas.

### Practice Exercises
- In `notes-week6-day1.md`, copy your `MONGODB_URI` (redacted) and annotate:
  - Where is the username?
  - Where is the password?
  - Where is the DB name?

### Reflection & Self‑Check
- In `notes-week6-day1.md`, answer:
  - What is a “collection” in MongoDB?
  - Why keep the connection string in `.env` instead of hard‑coding it?

### Optional Stretch
- In Atlas, use the UI to:
  - Create a collection named `notes`.
  - Insert one test document manually from the web interface.
- Tomorrow you’ll access similar documents via Mongoose.

---

## Day 2 – Defining a Note Model with Mongoose

### Goals
- Create a Mongoose schema and model for `Note`.
- Insert and retrieve notes via Mongoose (instead of in‑memory arrays).
- Understand basic schema fields and types.

### Concepts to Learn
- Mongoose Schema & Model:
  - `new mongoose.Schema({ ... })`
  - `mongoose.model("Note", noteSchema)`
- Common field types: `String`, `Boolean`, `Date`.
- Basic CRUD methods:
  - `Note.create`, `Note.find`.

### Step‑by‑Step Tasks

1. **Create `models/Note.js`**
   - In `models/Note.js`:

     ```js
     const mongoose = require("mongoose");

     const noteSchema = new mongoose.Schema(
       {
         title: {
           type: String,
           required: true,
           trim: true,
         },
         content: {
           type: String,
           required: true,
           trim: true,
         },
         // You could add more fields later, e.g. tags, userId, etc.
       },
       {
         timestamps: true, // adds createdAt and updatedAt automatically
       }
     );

     const Note = mongoose.model("Note", noteSchema);

     module.exports = Note;
     ```

2. **Replace in‑memory notes in `notesRoutes.js`**
   - In `notesRoutes.js`, remove your `let notes = [...]` array.
   - At top:

     ```js
     const express = require("express");
     const Note = require("./models/Note"); // adjust path as needed (../models/Note)
     const router = express.Router();
     ```

     If `notesRoutes.js` is in `routes/`, the path is:

     ```js
     const Note = require("../models/Note");
     ```

3. **Implement GET all notes using Mongoose**
   - Replace your `router.get("/")` with:

     ```js
     router.get("/", async (req, res, next) => {
       try {
         const notes = await Note.find().sort({ createdAt: -1 });
         res.json(notes);
       } catch (error) {
         next(error);
       }
     });
     ```

4. **Implement POST create note**
   - Replace your existing POST route with:

     ```js
     router.post("/", async (req, res, next) => {
       try {
         const { title, content } = req.body;
         if (!title || !content) {
           return res.status(400).json({ error: "Title and content are required" });
         }

         const newNote = await Note.create({ title, content });
         res.status(201).json(newNote);
       } catch (error) {
         next(error);
       }
     });
     ```

5. **Wire routes in `server.js`**
   - Ensure in `server.js` you have:

     ```js
     const notesRouter = require("./routes/notesRoutes");
     app.use("/api/notes", notesRouter);
     ```

6. **Test with Postman**
   - `GET http://localhost:3000/api/notes`:
     - Should return an empty array (if DB has no notes yet).
   - `POST http://localhost:3000/api/notes` with JSON body:

     ```json
     {
       "title": "Mongo Note",
       "content": "Saved in MongoDB via Mongoose."
     }
     ```

   - Then `GET` again; you should see the new note with `_id`, `createdAt`, `updatedAt`.

### Practice Exercises
- Try creating several notes with different titles/content.
- In Atlas UI, open your `notes` collection and see the documents appear.

### Reflection & Self‑Check
- In `notes-week6-day2.md`, answer:
  - What advantages do you immediately see in using MongoDB compared to an in‑memory array?
  - What does the `timestamps: true` option give you?

### Optional Stretch
- Add a `favorite` boolean field to the schema with default `false`:
  - Then create a note with `{ "favorite": true }` and confirm it’s saved correctly.

---

## Day 3 – Full CRUD with Mongoose (GET by ID, PATCH, DELETE)

### Goals
- Implement all CRUD endpoints using Mongoose.
- Learn how to handle invalid MongoDB IDs.
- Practice consistent error and status code usage.

### Concepts to Learn
- `Note.findById(id)`
- `Note.findByIdAndUpdate(id, update, options)`
- `Note.findByIdAndDelete(id)`
- Invalid ObjectId errors (`CastError`).

### Step‑by‑Step Tasks

1. **GET note by ID**
   - In `routes/notesRoutes.js`:

     ```js
     router.get("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const note = await Note.findById(id);
         if (!note) {
           return res.status(404).json({ error: "Note not found" });
         }
         res.json(note);
       } catch (error) {
         next(error);
       }
     });
     ```

2. **PATCH note**
   - Implement partial update:

     ```js
     router.patch("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const updates = req.body; // e.g., { title: "New title" }

         const updatedNote = await Note.findByIdAndUpdate(id, updates, {
           new: true, // return updated document
           runValidators: true, // apply schema validation
         });

         if (!updatedNote) {
           return res.status(404).json({ error: "Note not found" });
         }

         res.json(updatedNote);
       } catch (error) {
         next(error);
       }
     });
     ```

3. **DELETE note**
   - Add:

     ```js
     router.delete("/:id", async (req, res, next) => {
       try {
         const { id } = req.params;
         const deleted = await Note.findByIdAndDelete(id);
         if (!deleted) {
           return res.status(404).json({ error: "Note not found" });
         }
         res.status(204).send();
       } catch (error) {
         next(error);
       }
     });
     ```

4. **Error handling for invalid IDs (`CastError`)**
   - In `server.js`, refine your error handler:

     ```js
     app.use((err, req, res, next) => {
       console.error(err);

       if (err.name === "CastError" && err.kind === "ObjectId") {
         return res.status(400).json({ error: "Invalid ID format" });
       }

       if (err.name === "ValidationError") {
         return res.status(400).json({ error: err.message });
       }

       res.status(500).json({ error: "Internal server error" });
     });
     ```

5. **Test with Postman**
   - Create multiple notes.
   - Test:
     - `GET /api/notes/:id` for a real `_id` and a random invalid one:
       - For a badly formatted ID (e.g., `123`), you should get 400.
     - `PATCH /api/notes/:id`:
       - Change title, verify.
     - `DELETE /api/notes/:id`:
       - Delete and confirm via `GET`.

### Practice Exercises
- In POST route, add a simple title length check (e.g., min 3 chars), and trigger a 400 if too short.
- Try sending an empty update object to PATCH and decide how your API should respond (e.g., 400).

### Reflection & Self‑Check
- In `notes-week6-day3.md`, answer:
  - How is `findByIdAndUpdate` different from `findById` + `save`?
  - Why do we return 204 (No Content) on DELETE?

### Optional Stretch
- Add a route `GET /api/notes/favorites` that returns all notes where `favorite: true`.

---

## Day 4 – Better Structure, Validation & Config

### Goals
- Clean up your project structure.
- Centralize DB connection logic.
- Improve validation and config handling.

### Concepts to Learn
- Separation of concerns:
  - `models/`, `routes/`, potential `controllers/`, `config/`.
- Validation using Mongoose & manual checks.
- Clean startup flow: connect DB → start server.

### Step‑by‑Step Tasks

1. **Refactor DB connection**
   - Create `config/db.js`:

     ```js
     const mongoose = require("mongoose");

     async function connectDB(uri) {
       try {
         await mongoose.connect(uri);
         console.log("Connected to MongoDB");
       } catch (error) {
         console.error("MongoDB connection error:", error.message);
         process.exit(1);
       }
     }

     module.exports = connectDB;
     ```

   - In `server.js`:

     ```js
     require("dotenv").config();
     const express = require("express");
     const connectDB = require("./config/db");

     const app = express();
     const PORT = process.env.PORT || 3000;

     app.use(express.json());

     const notesRouter = require("./routes/notesRoutes");
     app.use("/api/notes", notesRouter);

     // 404 & error handlers here...

     async function start() {
       await connectDB(process.env.MONGODB_URI);
       app.listen(PORT, () => {
         console.log(`Server running on http://localhost:${PORT}`);
       });
     }

     start();
     ```

2. **Improve Mongoose validation**
   - In `models/Note.js`, adjust fields:

     ```js
     const noteSchema = new mongoose.Schema(
       {
         title: {
           type: String,
           required: [true, "Title is required"],
           trim: true,
           minlength: [3, "Title must be at least 3 characters"],
         },
         content: {
           type: String,
           required: [true, "Content is required"],
           trim: true,
           minlength: [5, "Content must be at least 5 characters"],
         },
       },
       { timestamps: true }
     );
     ```

   - In your error handler (server.js), you already handle `ValidationError`; test it by sending too short title/content.

3. **Clean up routes file**
   - Ensure `routes/notesRoutes.js` only handles:
     - Parsing params/body (lightly).
     - Calling Mongoose.
     - Sending JSON responses.
   - If you find complex logic repeating, consider moving to small helper functions, but don’t over‑engineer yet.

4. **Test everything again**
   - Run `npm run dev`.
   - Use Postman to:
     - Create valid and invalid notes.
     - See helpful error messages when validation fails.

### Practice Exercises
- Add a `maxLength` to `title` (e.g., 100 characters) and test it.
- Change default sort order in GET all notes (e.g., alphabetical by title) and see the difference.

### Reflection & Self‑Check
- In `notes-week6-day4.md`, answer:
  - In your own words, what is the role of Mongoose in your app?
  - What are the pros and cons of relying on schema validation vs manual validation in routes?

### Optional Stretch
- Add a `category` field to `Note` (e.g., `"work"`, `"personal"`).
- Add `GET /api/notes?category=work` to filter by category using `Note.find({ category: req.query.category })`.

---

## Day 5 – Frontend Client for Notes (with Mongo‑backed API)

### Goals
- Build a simple frontend that works with your Mongo‑backed API.
- Display, create, update (optional), delete notes via the browser.
- Practice mapping `_id` to front‑end logic.

### Concepts to Learn
- Using `_id` in frontend.
- Reusing Week 4 DOM skills with a real backend.

### Step‑by‑Step Tasks

1. **Create frontend files**
   - Inside project root, ensure you have:

     - `public/index.html`
     - `public/app.js`
     - `public/styles.css` (optional)

   - Simple `public/index.html`:

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1.0" />
       <title>Notes App</title>
       <link rel="stylesheet" href="styles.css" />
     </head>
     <body>
       <main class="notes-app">
         <h1>Notes</h1>
         <form id="note-form">
           <input id="note-title" type="text" placeholder="Title" />
           <textarea id="note-content" placeholder="Content"></textarea>
           <button type="submit">Add Note</button>
         </form>
         <ul id="notes-list"></ul>
       </main>
       <script src="app.js"></script>
     </body>
     </html>
     ```

2. **Serve static files**
   - In `server.js`, add before routes:

     ```js
     const path = require("path");
     app.use(express.static(path.join(__dirname, "public")));
     ```

3. **Basic styles (optional but helpful)**
   - In `public/styles.css`, at least:

     ```css
     body {
       font-family: Arial, sans-serif;
       margin: 0;
       background-color: #f4f4f4;
     }

     .notes-app {
       max-width: 600px;
       margin: 20px auto;
       padding: 20px;
       background: white;
       border-radius: 8px;
       box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
     }

     #note-form {
       display: flex;
       flex-direction: column;
       gap: 8px;
       margin-bottom: 16px;
     }

     #notes-list {
       list-style: none;
       padding: 0;
       margin: 0;
     }

     .note-item {
       border-bottom: 1px solid #eee;
       padding: 8px 0;
       display: flex;
       justify-content: space-between;
       gap: 8px;
     }

     .note-title {
       font-weight: bold;
     }
     ```

4. **Frontend JS (`public/app.js`)**
   - Implement fetch + render (similar to Week 5, but with `_id`):

     ```js
     const notesList = document.querySelector("#notes-list");
     const noteForm = document.querySelector("#note-form");
     const titleInput = document.querySelector("#note-title");
     const contentInput = document.querySelector("#note-content");

     async function fetchNotes() {
       notesList.innerHTML = "<li>Loading...</li>";
       try {
         const res = await fetch("/api/notes");
         if (!res.ok) throw new Error("Failed to fetch notes");
         const notes = await res.json();
         renderNotes(notes);
       } catch (error) {
         console.error(error);
         notesList.innerHTML = "<li>Error loading notes</li>";
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
       if (!title || !content) return;

       try {
         const res = await fetch("/api/notes", {
           method: "POST",
           headers: { "Content-Type": "application/json" },
           body: JSON.stringify({ title, content }),
         });
         if (!res.ok) throw new Error("Failed to create note");
         titleInput.value = "";
         contentInput.value = "";
         fetchNotes();
       } catch (error) {
         console.error(error);
       }
     });

     notesList.addEventListener("click", async (event) => {
       const target = event.target;
       if (!target.classList.contains("delete-btn")) return;

       const li = target.closest(".note-item");
       const id = li.dataset.id;

       try {
         const res = await fetch(`/api/notes/${id}`, { method: "DELETE" });
         if (res.status !== 204) {
           throw new Error("Failed to delete note");
         }
         fetchNotes();
       } catch (error) {
         console.error(error);
       }
     });

     fetchNotes();
     ```

5. **Test in browser**
   - Run `npm run dev`.
   - Go to `http://localhost:3000/`.
   - Add notes, delete notes; confirm changes persist (check in Atlas or Postman).

### Practice Exercises
- Show `createdAt` (formatted) under each note.
  - Hint: `new Date(note.createdAt).toLocaleString()`.

### Reflection & Self‑Check
- In `notes-week6-day5.md`, answer:
  - What differences did you notice between using `_id` from Mongo vs numeric ids?
  - Describe the full flow when you click the “Delete” button in the browser.

### Optional Stretch
- Add a simple “Edit” feature:
  - Button that opens `prompt()` with current content and PATCHes the note.

---

## Day 6 – Filtering, Sorting & Query Parameters

### Goals
- Implement basic filtering and sorting in your API.
- Use query parameters to control what the client sees.
- Practice thinking about server‑side vs client‑side responsibilities.

### Concepts to Learn
- Using `req.query` to read query params.
- Mongoose queries with filters and sorts:
  - `Note.find({ title: /something/i })`
  - `.sort({ createdAt: -1 })`.

### Step‑by‑Step Tasks

1. **Add search/filter to GET /api/notes**
   - In `routes/notesRoutes.js`, modify GET all route:

     ```js
     router.get("/", async (req, res, next) => {
       try {
         const { search } = req.query;
         const query = {};

         if (search) {
           // Case-insensitive match in title or content
           query.$or = [
             { title: { $regex: search, $options: "i" } },
             { content: { $regex: search, $options: "i" } },
           ];
         }

         const notes = await Note.find(query).sort({ createdAt: -1 });
         res.json(notes);
       } catch (error) {
         next(error);
       }
     });
     ```

   - Test with:
     - `GET /api/notes`
     - `GET /api/notes?search=express`
     - `GET /api/notes?search=mongo`

2. **Add frontend search bar**
   - In `public/index.html`, above `<ul>`:

     ```html
     <input id="search-input" type="text" placeholder="Search notes..." />
     <button id="search-btn">Search</button>
     ```

   - In `public/app.js`:

     ```js
     const searchInput = document.querySelector("#search-input");
     const searchBtn = document.querySelector("#search-btn");

     async function fetchNotes(searchText = "") {
       notesList.innerHTML = "<li>Loading...</li>";
       try {
         const url = searchText
           ? `/api/notes?search=${encodeURIComponent(searchText)}`
           : "/api/notes";
         const res = await fetch(url);
         if (!res.ok) throw new Error("Failed to fetch notes");
         const notes = await res.json();
         renderNotes(notes);
       } catch (error) {
         console.error(error);
         notesList.innerHTML = "<li>Error loading notes</li>";
       }
     }

     searchBtn.addEventListener("click", () => {
       const text = searchInput.value.trim();
       fetchNotes(text);
     });
     ```

   - Now you can search notes from the frontend.

3. **(Optional) Live search**
   - Add `keyup` handler on `searchInput` to call `fetchNotes` after user stops typing (you can keep it simple: call directly on each keyup for now).

### Practice Exercises
- Add a query parameter `?sort=oldest` to sort ascending by `createdAt`:
  - If `req.query.sort === "oldest"`, use `.sort({ createdAt: 1 })`, else default to `-1`.

### Reflection & Self‑Check
- In `notes-week6-day6.md`, answer:
  - In your opinion, what should be done on the server and what should be done on the client when it comes to filtering/sorting?
  - How does your API behave if `search` is empty or missing?

### Optional Stretch
- Add a simple pagination:
  - `GET /api/notes?page=1&limit=5`
  - Use `.skip((page - 1) * limit).limit(limit)` in the Mongoose query.

---

## Day 7 – Review & Mini “Second Collection” Exercise

### Goals
- Review everything: MongoDB, Mongoose, CRUD, frontend integration.
- Design a second Mongo‑backed resource (e.g., `tasks` or `tags`) to solidify understanding.
- Reflect on what feels clear vs confusing.

### Concepts to Reinforce
- Designing schemas.
- Connecting models to routes.
- Testing and debugging DB‑backed APIs.

### Step‑by‑Step Tasks

1. **Quick written review**
   - In `notes-week6-day7.md`, write (in your words):
     - What is Mongoose?
     - What is a schema?
     - What is a model?
     - How does your app change when data moves from memory to Mongo?

2. **Design a small second collection**
   - Choose something simple:
     - `Task` with `{ title, completed, dueDate? }`
     - `Tag` with `{ name, color }`
   - In the same project, create:
     - `models/Task.js` or `models/Tag.js`.
     - A corresponding routes file `routes/tasksRoutes.js` or similar.

3. **Implement minimal CRUD for the new resource**
   - At minimum:
     - `GET /api/tasks`
     - `POST /api/tasks`
     - `PATCH /api/tasks/:id`
     - `DELETE /api/tasks/:id`
   - Use the same structure and patterns as `notesRoutes.js`.

4. **Test via Postman**
   - Create, read, update, delete a few items.
   - Confirm they show in Atlas.

5. **(Optional) Very simple frontend**
   - If you have time, create a quick HTML page `public/tasks.html` with:
     - A form to add tasks.
     - A list similar to notes.
   - Wire it up with `fetch` like you did for notes.

### Practice Exercises
- Add validation to your new model (e.g., title required, min length).
- Add a query filter (e.g., `?completed=true` for tasks).

### Reflection & Self‑Check (end of Week 6)
- In `notes-week6-day7.md`, also answer:
  - What was the hardest concept this week? (Connection strings? Schemas? Async/await?)
  - If your API suddenly stopped working, which 3 things would you check first?
  - How comfortable do you feel adding a new field to your Note schema and wiring it through routes + frontend?

### Optional Stretch
- Think about next step: adding **users** and **authentication**.
  - Sketch (just in notes) how a `User` schema might look (`email`, `passwordHash`).
  - Sketch how each Note might belong to a user (`userId` field referencing User).

---

By the end of Week 6 you should be able to:

- Connect an Express app to MongoDB using Mongoose.
- Define schemas and models for your data.
- Implement full CRUD with Mongoose methods.
- Handle common errors (404, validation, invalid IDs).
- Build a simple frontend that works with a real database‑backed API.

This sets you up for the next major step: **authentication and multi‑user apps** (Weeks 8–10), with Week 7 often used for consolidation or a small intermediate project.
