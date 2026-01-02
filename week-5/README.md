### Week 5 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 5:**  
Move from browser‑only JavaScript to **server‑side JavaScript** with **Node.js + Express**. You’ll learn how HTTP APIs work, and by the end of the week you’ll have a small **in‑memory REST API** that you can test with Postman and from a frontend.

Assume ~1.5–3 hours/day. Use extra time on practice/optional sections.

Folder for this week:

- `week5-node-express/`
  - `package.json`
  - `server.js` (or `index.js`)
  - Later: route files, etc.

If you don’t have Node yet, install it first from nodejs.org (LTS version).

---

## Day 1 – Node.js Basics & NPM

### Goals
- Install/check Node and npm.
- Understand what Node.js is (and how it differs from browser JS).
- Run simple Node scripts.
- Initialize a basic project with `npm`.

### Concepts to Learn
- Node.js = JS runtime outside the browser.
- Browser vs Node:
  - Browser has `document`, `window`, DOM; Node does NOT.
  - Node has `fs`, `http`, etc.; browser does NOT.
- `node file.js` to run scripts.
- `npm init -y` to create `package.json`.

### Step‑by‑Step Tasks

1. **Check Node & npm**
   - In your terminal:

     ```bash
     node -v
     npm -v
     ```

   - If these fail, install Node (which includes npm) and retry.

2. **Create project folder**
   - Make: `week5-node-express`.
   - Open it in VS Code.
   - In terminal (inside this folder):

     ```bash
     npm init -y
     ```

   - This creates `package.json`. Open it and skim; note `"main"` and `"scripts"`.

3. **First Node script**
   - Create `day1-hello.js`:

     ```js
     console.log("Hello from Node!");
     const name = "Your Name";
     console.log("Running this file as:", name);
     ```

   - Run:

     ```bash
     node day1-hello.js
     ```

4. **Node vs Browser check**
   - Append to `day1-hello.js`:

     ```js
     console.log("typeof window:", typeof window);
     console.log("typeof document:", typeof document);
     ```

   - Run again. You should see `undefined` – there’s no DOM in Node.
   - In your browser console (on any page), run the same lines:
     - You’ll see `object` for both.

5. **Use built‑in `fs` module (tiny intro)**
   - In `day1-hello.js`:

     ```js
     const fs = require("fs");

     fs.writeFileSync("test.txt", "This file was created by Node.");
     console.log("Created test.txt");
     ```

   - Run `node day1-hello.js`.
   - Check your folder; `test.txt` should exist.

### Practice Exercises
- Create `day1-practice.js`:
  - Define an array of numbers.
  - Compute the sum and average (reusing Week 3 logic).
  - `console.log` the result.
- Run it with Node.

### Reflection & Self‑Check
Create `notes-week5-day1.md`:
- In your own words:
  - What is Node.js?
  - What can Node do that browser JS cannot?
- Why is there no `document` in Node?

### Optional Stretch
- In `package.json`, add a script:

  ```json
  "scripts": {
    "hello": "node day1-hello.js"
  }
  ```

- Then run:

  ```bash
  npm run hello
  ```

---

## Day 2 – HTTP & Your First Express Server

### Goals
- Understand HTTP at a high level.
- Install Express and create a basic web server.
- Create simple GET routes that return text and JSON.

### Concepts to Learn
- HTTP request/response.
- Methods: `GET`, `POST`, `PUT`, `DELETE`.
- Express basics:
  - `const express = require("express")`
  - `app.get("/", handler)`
  - `app.listen(port)`

### Step‑by‑Step Tasks

1. **Install Express**
   - In `week5-node-express`:

     ```bash
     npm install express
     ```

2. **Create `server.js`**
   - Add:

     ```js
     const express = require("express");
     const app = express();

     const PORT = 3000;

     app.get("/", (req, res) => {
       res.send("Hello from Express server!");
     });

     app.get("/api/hello", (req, res) => {
       res.json({ message: "Hello, API client!", time: new Date().toISOString() });
     });

     app.listen(PORT, () => {
       console.log(`Server is running on http://localhost:${PORT}`);
     });
     ```

3. **Run the server**
   - In terminal:

     ```bash
     node server.js
     ```

   - Open `http://localhost:3000/` in your browser.
   - Open `http://localhost:3000/api/hello`.

4. **Explore `req` and `res`**
   - Add a route with path params:

     ```js
     app.get("/api/hello/:name", (req, res) => {
       const name = req.params.name;
       res.json({ message: `Hello, ${name}!` });
     });
     ```

   - Visit `http://localhost:3000/api/hello/Alice` in your browser.

### Practice Exercises
- Add a route `/api/time` that returns current time and date as JSON.
- Add a route `/api/square/:number` that returns `{ number, squared }`.

### Reflection & Self‑Check
In `notes-week5-day2.md`:
- What is an HTTP endpoint?
- What’s the difference between returning text with `res.send` and JSON with `res.json`?

### Optional Stretch
- Add a route `/api/greet` that uses query parameters, e.g.:
  - `http://localhost:3000/api/greet?name=Bob&lang=en`
  - Access them with `req.query`.

---

## Day 3 – Building a Simple In‑Memory REST API (GET & POST)

### Goals
- Model data on the server as an array of objects.
- Return collections and individual items with `GET`.
- Accept data via `POST` with JSON body.
- Use Postman or Insomnia to test API requests.

### Concepts to Learn
- JSON request body.
- `app.use(express.json())` middleware.
- Status codes: 200, 201, 400, 404.
- Basic REST patterns:
  - `/api/items` (collection)
  - `/api/items/:id` (single item)

### Step‑by‑Step Tasks

1. **Setup JSON body parsing**
   - At the top of `server.js`, after `const app = express();`:

     ```js
     app.use(express.json());
     ```

2. **Create an in‑memory list**
   - Add:

     ```js
     let notes = [
       { id: 1, title: "First Note", content: "This is my first note." },
       { id: 2, title: "Second Note", content: "Express is cool." },
     ];
     ```

3. **GET all notes**
   - Add:

     ```js
     app.get("/api/notes", (req, res) => {
       res.json(notes);
     });
     ```

   - Test via browser or Postman: `GET /api/notes`.

4. **GET note by id**
   - Add:

     ```js
     app.get("/api/notes/:id", (req, res) => {
       const id = Number(req.params.id);
       const note = notes.find((n) => n.id === id);

       if (!note) {
         return res.status(404).json({ error: "Note not found" });
       }

       res.json(note);
     });
     ```

5. **POST create a note**
   - Add:

     ```js
     app.post("/api/notes", (req, res) => {
       const { title, content } = req.body;

       if (!title || !content) {
         return res.status(400).json({ error: "Title and content are required" });
       }

       const newNote = {
         id: notes.length === 0 ? 1 : notes[notes.length - 1].id + 1,
         title,
         content,
       };

       notes.push(newNote);
       res.status(201).json(newNote);
     });
     ```

6. **Test with Postman/Insomnia**
   - Install Postman or Insomnia if you haven’t.
   - Create a `POST http://localhost:3000/api/notes` request.
   - In Body → raw → JSON:

     ```json
     {
       "title": "API Note",
       "content": "Created via POST request"
     }
     ```

   - Send and verify response + new note in `GET /api/notes`.

### Practice Exercises
- Add validation:
  - If `title` is too short (e.g., `< 3` characters), return `400` with error.
- Add a `createdAt` field for new notes using `new Date().toISOString()`.

### Reflection & Self‑Check
In `notes-week5-day3.md`:
- What does `app.use(express.json())` do?
- Why do we send 201 for a successful POST that creates something?

### Optional Stretch
- Add a `GET /api/notes/search?query=...` endpoint that filters notes where `title` or `content` includes the query text (case‑insensitive).

---

## Day 4 – Updating & Deleting (PUT/PATCH/DELETE) + Error Handling

### Goals
- Implement update and delete endpoints.
- Practice using correct HTTP methods and status codes.
- Add basic centralized error handling.

### Concepts to Learn
- PUT vs PATCH (high level: full vs partial update; you can implement either).
- `DELETE /resource/:id`.
- Status codes: 204 (no content), 500 (server error).
- Error handling middleware.

### Step‑by‑Step Tasks

1. **PUT or PATCH to update a note**
   - Add (choose PATCH here):

     ```js
     app.patch("/api/notes/:id", (req, res) => {
       const id = Number(req.params.id);
       const { title, content } = req.body;

       const note = notes.find((n) => n.id === id);
       if (!note) {
         return res.status(404).json({ error: "Note not found" });
       }

       if (title !== undefined) {
         note.title = title;
       }
       if (content !== undefined) {
         note.content = content;
       }

       res.json(note);
     });
     ```

   - Test with Postman: PATCH `http://localhost:3000/api/notes/1` with JSON body.

2. **DELETE a note**
   - Add:

     ```js
     app.delete("/api/notes/:id", (req, res) => {
       const id = Number(req.params.id);
       const index = notes.findIndex((n) => n.id === id);

       if (index === -1) {
         return res.status(404).json({ error: "Note not found" });
       }

       notes.splice(index, 1);
       res.status(204).send(); // no content
     });
     ```

   - Test with Postman: DELETE a note, then GET all to confirm removal.

3. **Basic error handling middleware**
   - Add near the bottom of `server.js`, before `app.listen`:

     ```js
     // 404 handler for unknown routes
     app.use((req, res, next) => {
       res.status(404).json({ error: "Route not found" });
     });

     // Error handler
     app.use((err, req, res, next) => {
       console.error("Unexpected error:", err);
       res.status(500).json({ error: "Internal server error" });
     });
     ```

   - To see it in action, you can manually `throw new Error("Test")` inside a route and see the 500 response.

### Practice Exercises
- In `PATCH /api/notes/:id`, add validation:
  - If both `title` and `content` are absent in body → `400` with appropriate message.
- Try to delete a non‑existent id and confirm you get 404.

### Reflection & Self‑Check
In `notes-week5-day4.md`:
- Why might we use PATCH instead of PUT?
- What’s the purpose of the “404 handler” middleware?

### Optional Stretch
- Add a simple logging middleware at the top:

  ```js
  app.use((req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
  });
  ```

- Confirm that every request is logged.

---

## Day 5 – Project Structure, Nodemon & Environment Variables

### Goals
- Organize your code a bit better.
- Use `nodemon` for automatic restarts.
- Introduce environment variables (e.g., for port).

### Concepts to Learn
- Project structure:
  - Separate routes and server startup.
- Nodemon:
  - Auto‑restarts on file changes.
- Environment variables & `process.env`.

### Step‑by‑Step Tasks

1. **Install nodemon (dev dependency)**
   - In terminal:

     ```bash
     npm install --save-dev nodemon
     ```

2. **Add npm scripts**
   - In `package.json`:

     ```json
     "scripts": {
       "start": "node server.js",
       "dev": "nodemon server.js"
     }
     ```

   - Run:

     ```bash
     npm run dev
     ```

   - Confirm server auto‑restarts when you save changes.

3. **Use environment variable for PORT**
   - In `server.js`:

     ```js
     const PORT = process.env.PORT || 3000;
     ```

   - Run:

     ```bash
     PORT=4000 npm run dev
     ```

   - (On Windows Command Prompt, set env differently; you can skip custom PORT and just use default if this is confusing.)

4. **Refactor routes into a separate file (basic)**
   - Create `notesRoutes.js`:

     ```js
     const express = require("express");
     const router = express.Router();

     let notes = [
       { id: 1, title: "First Note", content: "This is my first note." },
       { id: 2, title: "Second Note", content: "Express is cool." },
     ];

     // define routes using router.get/post/etc
     router.get("/", (req, res) => {
       res.json(notes);
     });

     router.get("/:id", (req, res) => {
       const id = Number(req.params.id);
       const note = notes.find((n) => n.id === id);
       if (!note) return res.status(404).json({ error: "Note not found" });
       res.json(note);
     });

     // Add POST/PATCH/DELETE similarly (copy from server.js and adjust paths)

     module.exports = router;
     ```

   - In `server.js`, remove the notes routes and instead:

     ```js
     const express = require("express");
     const app = express();
     const notesRouter = require("./notesRoutes");

     app.use(express.json());

     app.use("/api/notes", notesRouter);

     // 404 + error handlers + listen...
     ```

5. **Test again**
   - Restart `npm run dev` if needed.
   - Test all `GET/POST/PATCH/DELETE` endpoints via Postman.

### Practice Exercises
- Add a second router file (e.g., `statusRoutes.js`) with:
  - `/api/status` → `{ status: "ok", uptime: process.uptime() }`.
- Mount it in `server.js` with `app.use("/api/status", statusRouter);`.

### Reflection & Self‑Check
In `notes-week5-day5.md`:
- What benefits do you see from splitting routes into their own file?
- What problem does nodemon solve?

### Optional Stretch
- Install `dotenv` and load a `.env` file:
  - `npm install dotenv`
  - Create `.env` with `PORT=5000`.
  - In `server.js`:

    ```js
    require("dotenv").config();
    const PORT = process.env.PORT || 3000;
    ```

---

## Day 6 – Connecting Frontend to Your API (Fetch from Browser)

### Goals
- Call your Express API from a simple frontend page using `fetch`.
- Understand CORS basics.
- See full flow: browser → server → JSON → browser.

### Concepts to Learn
- `fetch(url, options)` from the browser.
- CORS (just enough to get past basic errors).
- Using your Week 4 skills to build a minimal UI for notes.

### Step‑by‑Step Tasks

1. **Create a simple frontend for notes**
   - In `week5-node-express`, create:
     - `public/index.html`
     - `public/app.js`
     - `public/styles.css` (optional)

   - For now, simplest `index.html`:

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1.0" />
       <title>Notes Frontend</title>
     </head>
     <body>
       <h1>Notes</h1>

       <form id="note-form">
         <input id="note-title" type="text" placeholder="Title" />
         <input id="note-content" type="text" placeholder="Content" />
         <button type="submit">Add Note</button>
       </form>

       <ul id="notes-list"></ul>

       <script src="app.js"></script>
     </body>
     </html>
     ```

2. **Serve static files with Express**
   - In `server.js`, add:

     ```js
     const path = require("path");

     app.use(express.static(path.join(__dirname, "public")));
     ```

   - Now `http://localhost:3000/` should serve `public/index.html`.

3. **Basic fetch logic in `public/app.js`**
   - In `app.js`:

     ```js
     const notesList = document.querySelector("#notes-list");
     const noteForm = document.querySelector("#note-form");
     const titleInput = document.querySelector("#note-title");
     const contentInput = document.querySelector("#note-content");

     async function fetchNotes() {
       try {
         const res = await fetch("/api/notes");
         if (!res.ok) {
           throw new Error("Failed to fetch notes");
         }
         const data = await res.json();
         renderNotes(data);
       } catch (error) {
         console.error(error);
         notesList.innerHTML = "<li>Error loading notes</li>";
       }
     }

     function renderNotes(notes) {
       notesList.innerHTML = "";
       notes.forEach((note) => {
         const li = document.createElement("li");
         li.textContent = `${note.id}: ${note.title} - ${note.content}`;
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
           headers: {
             "Content-Type": "application/json",
           },
           body: JSON.stringify({ title, content }),
         });

         if (!res.ok) {
           throw new Error("Failed to create note");
         }

         titleInput.value = "";
         contentInput.value = "";
         fetchNotes();
       } catch (error) {
         console.error(error);
       }
     });

     fetchNotes();
     ```

4. **Test**
   - Run `npm run dev`.
   - Visit `http://localhost:3000/`.
   - Add notes via form; confirm they appear and that `GET /api/notes` shows them in Postman too.

### Practice Exercises
- Add a “Reload Notes” button that calls `fetchNotes()` when clicked.
- Show a basic loading message while fetching (e.g., set `notesList.innerHTML = "<li>Loading...</li>"` before the fetch).

### Reflection & Self‑Check
In `notes-week5-day6.md`:
- Describe the full journey when you submit the note form:
  - From clicking submit to seeing the new note in the list.
- What happens if the server is offline and you call `fetch`?

### Optional Stretch
- Use DevTools Network tab to inspect:
  - Request method, URL, headers, body.
  - Response status and JSON.
- Note any differences between this and requests in Postman.

---

## Day 7 – Review & Mini API Project

### Goals
- Solidify your understanding by building a small API from scratch (no copy‑paste).
- Practice designing endpoints and testing them.
- Reflect on your mental model of client–server interactions.

### Concepts to Reinforce
- Full CRUD lifecycle: GET, POST, PATCH/PUT, DELETE.
- Status codes and error responses.
- Separating data, routes, and app bootstrap.

### Step‑by‑Step Tasks

1. **Pick a small resource to model**
   - Examples:
     - `tasks` (different from your Week 4 todos).
     - `books`
     - `movies`
   - In `notes-week5-day7.md`, quickly outline:
     - Fields (e.g., for books: `id`, `title`, `author`, `year`).
     - Endpoints you’ll have (GET all, GET by id, POST, PATCH, DELETE).

2. **Create a new file (e.g., `booksRoutes.js`)**
   - Implement:
     - In‑memory array of resources.
     - `GET /api/books` → all.
     - `GET /api/books/:id` → by id.
     - `POST /api/books` → create.
     - `PATCH /api/books/:id` → partial update.
     - `DELETE /api/books/:id` → delete.
   - Mount it in `server.js` similarly to notes.

3. **Test all endpoints with Postman**
   - For each:
     - Write down:
       - Request type and path.
       - Expected status code.
       - Example request body (for POST/PATCH).
   - Actually send those requests and see if responses match expectations.

4. **(Optional) Connect a tiny frontend**
   - If time allows, copy your `public` setup to a new HTML page (e.g., `books.html`) and:
     - Fetch and display books.
     - Form to add a new book.

5. **Final review**
   - Skim through:
     - `server.js`
     - `notesRoutes.js`
     - Your new `booksRoutes.js`
   - Check for:
     - Clear function names.
     - Consistent error messages.
     - Correct HTTP codes.

### Practice Exercises
- For your new resource:
  - Add simple validation in POST route (e.g., title is required).
  - Add a query parameter filter (e.g., `/api/books?author=...`).

### Reflection & Self‑Check
In `notes-week5-day7.md`, also answer:
- Can you describe, in plain language, what Express is doing for you?
- What steps would you take to debug a 500 error in one of your endpoints?
- Which part of REST APIs feels most natural so far? Which part is still fuzzy?

### Optional Stretch
- Try to intentionally break your API (e.g., throw an error in a route) and confirm your error middleware catches it and returns 500.
- Think ahead: how would this change if you stored data in a database instead of an in‑memory array?

---

By the end of Week 5, you will:

- Understand Node vs browser JS.
- Run an Express server with multiple routes.
- Expose a small REST API with full CRUD over an in‑memory collection.
- Test endpoints with Postman and from a frontend using `fetch`.
- Have a basic mental model of client–server HTTP communication.

This sets you up for Week 6, where you’ll add a database (MongoDB) so your data persists beyond server restarts.
