### Week 9 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 9:**  
Move your app’s frontend to **React**:

- Learn React fundamentals (components, props, state, `useEffect`)
- Set up a modern React tooling setup (Vite)
- Build a **React client** for your existing authenticated API (notes/tasks)
- Implement login, logout, and a protected “dashboard” screen

Assume ~1.5–3 hours/day. Use extra time on practice/optional parts.

I’ll assume you have:

- A backend from Week 8: **Express + MongoDB + JWT auth**  
  - `/api/auth/register`, `/api/auth/login`, `/api/auth/me`
  - `/api/notes` (or `/api/tasks`) protected CRUD, tied to `req.user`
  - CORS enabled for some origin(s)

This week you’ll:

- Create a new React project (e.g., `week9-react-client`)
- Gradually hook it up to your backend

---

## Day 1 – React Setup & Mental Model

### Goals
- Install and run a basic React app.
- Understand the high‑level React mental model.
- Create your first custom components and JSX.

### Concepts to Learn
- React = UI library for building component trees.
- JSX = syntax extension that looks like HTML in JS.
- Where the app starts: `main.jsx` / `index.js` mounting `<App />`.

### Step‑by‑Step Tasks

1. **Create a React app using Vite**
   - In a terminal (not inside your backend project):

     ```bash
     npm create vite@latest week9-react-client -- --template react
     cd week9-react-client
     npm install
     npm run dev
     ```

   - Open the URL shown (usually `http://localhost:5173`).

2. **Explore the project structure**
   - Open `week9-react-client` in VS Code.
   - Inspect:
     - `index.html` – root HTML file with `<div id="root"></div>`.
     - `src/main.jsx` – entry point; renders `<App />` into root.
     - `src/App.jsx` – main component.
   - Note how `main.jsx` looks:

     ```jsx
     import React from "react";
     import ReactDOM from "react-dom/client";
     import App from "./App.jsx";
     import "./index.css";

     ReactDOM.createRoot(document.getElementById("root")).render(
       <React.StrictMode>
         <App />
       </React.StrictMode>
     );
     ```

3. **Simplify `App.jsx`**
   - Replace the default content with something simple:

     ```jsx
     function App() {
       return (
         <div>
           <h1>Week 9 – React Client</h1>
           <p>Learning React fundamentals.</p>
         </div>
       );
     }

     export default App;
     ```

   - Confirm the browser updates automatically.

4. **Create your first custom component**
   - In `src/`, create `Header.jsx`:

     ```jsx
     function Header() {
       return (
         <header>
           <h1>My React App</h1>
           <p>A place to practice components.</p>
         </header>
       );
     }

     export default Header;
     ```

   - Update `App.jsx` to use it:

     ```jsx
     import Header from "./Header.jsx";

     function App() {
       return (
         <div>
           <Header />
           <main>
             <p>This is the main content area.</p>
           </main>
         </div>
       );
     }

     export default App;
     ```

### Practice Exercises
- Create a `Footer.jsx` component that displays your name and the current year.
- Use it in `App.jsx` below `<main>`.

### Reflection & Self‑Check
Create `notes-week9-day1.md`:
- In your own words:
  - What is a React “component”?
  - Where in the code does your app start rendering?

### Optional Stretch
- Add simple styling in `src/index.css` (center content, change font, etc.).
- Create a `Greeting.jsx` component that takes a `name` prop (you’ll wire props tomorrow).

---

## Day 2 – Components, Props & Lists

### Goals
- Understand **props** and component composition.
- Render dynamic lists with `.map` and keys.
- Structure your UI similar to your notes/tasks app using mock data.

### Concepts to Learn
- Props = read‑only inputs to components.
- Parent → child data flow.
- Rendering lists with `.map`.
- `key` prop to help React track list items.

### Step‑by‑Step Tasks

1. **Props basics**
   - Create `Greeting.jsx`:

     ```jsx
     function Greeting({ name }) {
       return <p>Hello, {name}!</p>;
     }

     export default Greeting;
     ```

   - In `App.jsx`:

     ```jsx
     import Header from "./Header.jsx";
     import Greeting from "./Greeting.jsx";

     function App() {
       return (
         <div>
           <Header />
           <main>
             <Greeting name="Alice" />
             <Greeting name="Bob" />
           </main>
         </div>
       );
     }

     export default App;
     ```

2. **Mock data for notes/tasks**
   - In `App.jsx`, create some sample data:

     ```jsx
     const mockNotes = [
       { id: 1, title: "Learn React", content: "Practice components and hooks." },
       { id: 2, title: "Connect API", content: "Use fetch with useEffect." },
     ];
     ```

3. **Create a `NoteItem` and `NoteList`**
   - `src/NoteItem.jsx`:

     ```jsx
     function NoteItem({ note }) {
       return (
         <li>
           <h3>{note.title}</h3>
           <p>{note.content}</p>
         </li>
       );
     }

     export default NoteItem;
     ```

   - `src/NoteList.jsx`:

     ```jsx
     import NoteItem from "./NoteItem.jsx";

     function NoteList({ notes }) {
       if (notes.length === 0) {
         return <p>No notes yet.</p>;
       }

       return (
         <ul>
           {notes.map((note) => (
             <NoteItem key={note.id} note={note} />
           ))}
         </ul>
       );
     }

     export default NoteList;
     ```

   - Use in `App.jsx`:

     ```jsx
     import NoteList from "./NoteList.jsx";

     function App() {
       const mockNotes = [
         { id: 1, title: "Learn React", content: "Practice components and hooks." },
         { id: 2, title: "Connect API", content: "Use fetch with useEffect." },
       ];

       return (
         <div>
           <Header />
           <main>
             <Greeting name="Alice" />
             <NoteList notes={mockNotes} />
           </main>
         </div>
       );
     }
     ```

4. **Play with different props**
   - Add a `createdAt` field to notes and display it in `NoteItem`.

### Practice Exercises
- Add a `NoteCount` component that receives `count` as a prop and renders `"You have X notes."`.
- Use it above the `<NoteList>` and pass `mockNotes.length`.

### Reflection & Self‑Check
In `notes-week9-day2.md`:
- How does data flow from `App` to `NoteItem`?
- Why does each list item need a `key`?

### Optional Stretch
- Create a `Layout` component that wraps `<Header>`, `<main>`, and `<Footer>`, and use it in `App` to keep `App` small.

---

## Day 3 – State, Events & Controlled Inputs

### Goals
- Manage component **state** with `useState`.
- Handle events like button clicks and form submissions.
- Build a simple in‑memory note/task form.

### Concepts to Learn
- `useState(initialValue)`:
  - Returns `[value, setValue]`.
- Controlled inputs:
  - `value={state}` + `onChange={...}`.
- Updating arrays immutably:
  - `setNotes([...notes, newNote])`
  - `setNotes(notes.filter(...))`

### Step‑by‑Step Tasks

1. **Basic counter example**
   - In `App.jsx`, temporarily:

     ```jsx
     import { useState } from "react";

     function App() {
       const [count, setCount] = useState(0);

       function handleIncrement() {
         setCount(count + 1);
       }

       return (
         <div>
           <Header />
           <main>
             <p>Count: {count}</p>
             <button onClick={handleIncrement}>Increment</button>
           </main>
         </div>
       );
     }
     ```

   - Click and see the count change.

2. **Stateful notes list**
   - Replace `mockNotes` with state:

     ```jsx
     const [notes, setNotes] = useState([
       { id: 1, title: "Learn React", content: "Practice components and hooks." },
     ]);
     ```

   - Pass `notes` to `<NoteList notes={notes} />`.

3. **Note form component**
   - Create `NoteForm.jsx`:

     ```jsx
     import { useState } from "react";

     function NoteForm({ onAddNote }) {
       const [title, setTitle] = useState("");
       const [content, setContent] = useState("");

       function handleSubmit(event) {
         event.preventDefault();
         const trimmedTitle = title.trim();
         const trimmedContent = content.trim();
         if (!trimmedTitle || !trimmedContent) return;

         onAddNote({ title: trimmedTitle, content: trimmedContent });
         setTitle("");
         setContent("");
       }

       return (
         <form onSubmit={handleSubmit}>
           <input
             type="text"
             placeholder="Title"
             value={title}
             onChange={(e) => setTitle(e.target.value)}
           />
           <textarea
             placeholder="Content"
             value={content}
             onChange={(e) => setContent(e.target.value)}
           />
           <button type="submit">Add Note</button>
         </form>
       );
     }

     export default NoteForm;
     ```

4. **Wire `NoteForm` to `App`**
   - In `App.jsx`:

     ```jsx
     import NoteForm from "./NoteForm.jsx";
     import NoteList from "./NoteList.jsx";
     import { useState } from "react";

     function App() {
       const [notes, setNotes] = useState([]);

       function handleAddNote({ title, content }) {
         const newNote = {
           id: notes.length === 0 ? 1 : notes[notes.length - 1].id + 1,
           title,
           content,
         };
         setNotes([...notes, newNote]);
       }

       return (
         <div>
           <Header />
           <main>
             <NoteForm onAddNote={handleAddNote} />
             <NoteList notes={notes} />
           </main>
         </div>
       );
     }
     ```

### Practice Exercises
- Add a “Delete” button to `NoteItem`:
  - Pass an `onDelete` prop from `NoteList` down to `NoteItem`.
  - In `App`, implement `handleDeleteNote(id)` and pass it to `NoteList`.

### Reflection & Self‑Check
In `notes-week9-day3.md`:
- What is a “controlled input”? Why is it useful?
- How do you update state based on the previous state (e.g., `setNotes([...notes, newNote])`)?

### Optional Stretch
- Add a “completed” boolean to notes and a button to toggle it in `NoteItem` (client‑side only for now).

---

## Day 4 – `useEffect` & Fetching Data from an API

### Goals
- Learn how to fetch data on component mount with `useEffect`.
- Handle loading and error states.
- Practice with a public API to avoid auth complexity at first.

### Concepts to Learn
- `useEffect(() => { ... }, [])` for run‑once side effects.
- Async data loading pattern:
  - `loading` state, `error` state, `data` state.
- Rendering different UI states.

### Step‑by‑Step Tasks

1. **Create a simple fetch example**
   - In `App.jsx`, create a new component `PostsPage` or similar:

     ```jsx
     import { useEffect, useState } from "react";

     function PostsPage() {
       const [posts, setPosts] = useState([]);
       const [loading, setLoading] = useState(false);
       const [error, setError] = useState("");

       useEffect(() => {
         async function fetchPosts() {
           setLoading(true);
           setError("");
           try {
             const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=5");
             if (!res.ok) {
               throw new Error("Failed to fetch posts");
             }
             const data = await res.json();
             setPosts(data);
           } catch (err) {
             setError(err.message);
           } finally {
             setLoading(false);
           }
         }

         fetchPosts();
       }, []);

       if (loading) return <p>Loading posts...</p>;
       if (error) return <p>Error: {error}</p>;

       return (
         <div>
           <h2>Sample Posts</h2>
           <ul>
             {posts.map((post) => (
               <li key={post.id}>
                 <strong>{post.title}</strong>
                 <p>{post.body}</p>
               </li>
             ))}
           </ul>
         </div>
       );
     }
     ```

   - Use it in `App` (either on its own page or under your notes UI).

2. **Understand effect dependencies**
   - Add a `console.log("Fetching posts...")` inside `fetchPosts`.
   - Confirm it runs only once on mount because dependency array is `[]`.

3. **Relate to your backend**
   - In `notes-week9-day4.md`, sketch how you’ll replace the URL with your backend’s URL (`http://localhost:3000/api/notes`) later, and how you’ll attach headers.

### Practice Exercises
- Change `_limit=5` to `_limit=10` and observe.
- Add a “Reload posts” button that re‑runs the fetch.

### Reflection & Self‑Check
In `notes-week9-day4.md`:
- In your own words, when does `useEffect` run?
- Why do we keep `loading`, `error`, and `data` states separate?

### Optional Stretch
- Add a text input that filters the displayed posts client‑side (similar to how you filtered notes earlier).

---

## Day 5 – React Router & Basic Auth Screens (Client Only)

### Goals
- Introduce **client‑side routing** with `react-router-dom`.
- Create separate pages: Login, Register, Dashboard.
- Mock auth state in React (no real backend calls yet) to understand the flow.

### Concepts to Learn
- SPA routing vs traditional navigation.
- `BrowserRouter`, `Routes`, `Route`.
- `Link` vs `<a>`.
- Basic “protected route” pattern.

### Step‑by‑Step Tasks

1. **Install React Router**
   - In `week9-react-client`:

     ```bash
     npm install react-router-dom
     ```

2. **Set up router in `main.jsx`**
   - Update `main.jsx`:

     ```jsx
     import React from "react";
     import ReactDOM from "react-dom/client";
     import { BrowserRouter } from "react-router-dom";
     import App from "./App.jsx";
     import "./index.css";

     ReactDOM.createRoot(document.getElementById("root")).render(
       <React.StrictMode>
         <BrowserRouter>
           <App />
         </BrowserRouter>
       </React.StrictMode>
     );
     ```

3. **Define basic routes**
   - In `App.jsx`:

     ```jsx
     import { Routes, Route, Link } from "react-router-dom";
     import { useState } from "react";

     function HomePage() {
       return <h2>Home</h2>;
     }

     function LoginPage() {
       return <h2>Login Page (we’ll wire this later)</h2>;
     }

     function RegisterPage() {
       return <h2>Register Page</h2>;
     }

     function DashboardPage() {
       return <h2>Dashboard (protected)</h2>;
     }

     function App() {
       const [isLoggedIn, setIsLoggedIn] = useState(false); // mock state for now

       return (
         <div>
           <nav>
             <Link to="/">Home</Link> | <Link to="/login">Login</Link> |{" "}
             <Link to="/register">Register</Link> | <Link to="/dashboard">Dashboard</Link>
           </nav>

           <Routes>
             <Route path="/" element={<HomePage />} />
             <Route path="/login" element={<LoginPage />} />
             <Route path="/register" element={<RegisterPage />} />
             <Route path="/dashboard" element={<DashboardPage />} />
           </Routes>
         </div>
       );
     }

     export default App;
     ```

4. **Create a simple ProtectedRoute component**
   - In `src/ProtectedRoute.jsx`:

     ```jsx
     import { Navigate } from "react-router-dom";

     function ProtectedRoute({ isLoggedIn, children }) {
       if (!isLoggedIn) {
         return <Navigate to="/login" replace />;
       }
       return children;
     }

     export default ProtectedRoute;
     ```

   - Use it in `App.jsx`:

     ```jsx
     import ProtectedRoute from "./ProtectedRoute.jsx";

     // in Routes:
     <Route
       path="/dashboard"
       element={
         <ProtectedRoute isLoggedIn={isLoggedIn}>
           <DashboardPage />
         </ProtectedRoute>
       }
     />
     ```

5. **Mock login behavior**
   - In `LoginPage`, add a button that sets `isLoggedIn` via prop:

     ```jsx
     function LoginPage({ onLogin }) {
       return (
         <div>
           <h2>Login Page</h2>
           <button onClick={onLogin}>Mock Login</button>
         </div>
       );
     }

     // In App:
     <Route
       path="/login"
       element={<LoginPage onLogin={() => setIsLoggedIn(true)} />}
     />
     ```

   - Now click “Mock Login” and try visiting `/dashboard`.

### Practice Exercises
- Add a “Logout” button in a nav bar that sets `isLoggedIn` to false and navigates to `/`.
- In `HomePage`, show a message depending on `isLoggedIn`.

### Reflection & Self‑Check
In `notes-week9-day5.md`:
- How is React Router different from traditional multi‑page navigation?
- In your own words, what does `<Navigate>` do?

### Optional Stretch
- Create a `Layout` route that wraps all pages with a common header/nav/footer.

---

## Day 6 – Real Auth: Connecting React Login/Register to Your Backend

### Goals
- Replace mock auth with **real login/register calls** to your backend.
- Store the JWT token in React state and localStorage.
- Use a **global auth context** to share auth state across components.

### Concepts to Learn
- Context API:
  - `createContext`, `AuthProvider`, `useContext`.
- Integrating React with your Node/Express JWT auth.
- Using `Authorization: Bearer <token>` in React fetch calls.

### Step‑by‑Step Tasks

1. **Adjust backend CORS to allow React dev origin**
   - In your backend `server.js`, ensure:

     ```js
     const cors = require("cors");
     app.use(
       cors({
         origin: "http://localhost:5173",
       })
     );
     ```

   - Restart backend.

2. **Create an `api.js` in React project**
   - In `week9-react-client/src/api.js`:

     ```jsx
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

     export async function fetchNotesApi() {
       const res = await fetch(`${API_BASE}/api/notes`, {
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

     // Add update/delete later as needed
     ```

3. **Create an AuthContext**
   - `src/AuthContext.jsx`:

     ```jsx
     import { createContext, useContext, useEffect, useState } from "react";
     import { setAuthToken, fetchMeApi } from "./api.js";

     const AuthContext = createContext(null);

     export function AuthProvider({ children }) {
       const [user, setUser] = useState(null);
       const [loading, setLoading] = useState(true);

       useEffect(() => {
         const token = localStorage.getItem("notesToken");
         if (!token) {
           setLoading(false);
           return;
         }

         setAuthToken(token);

         async function loadUser() {
           try {
             const me = await fetchMeApi();
             setUser(me);
           } catch (error) {
             console.error("Invalid token", error);
             localStorage.removeItem("notesToken");
             setAuthToken(null);
           } finally {
             setLoading(false);
           }
         }

         loadUser();
       }, []);

       function login(token, userData) {
         localStorage.setItem("notesToken", token);
         setAuthToken(token);
         setUser(userData);
       }

       function logout() {
         localStorage.removeItem("notesToken");
         setAuthToken(null);
         setUser(null);
       }

       return (
         <AuthContext.Provider value={{ user, login, logout, loading }}>
           {children}
         </AuthContext.Provider>
       );
     }

     export function useAuth() {
       const ctx = useContext(AuthContext);
       if (!ctx) {
         throw new Error("useAuth must be used within AuthProvider");
       }
       return ctx;
     }
     ```

4. **Wrap App with AuthProvider**
   - In `main.jsx`:

     ```jsx
     import { AuthProvider } from "./AuthContext.jsx";

     ReactDOM.createRoot(document.getElementById("root")).render(
       <React.StrictMode>
         <BrowserRouter>
           <AuthProvider>
             <App />
           </AuthProvider>
         </BrowserRouter>
       </React.StrictMode>
     );
     ```

5. **Use real login in `LoginPage`**
   - In `App.jsx` or a separate `LoginPage.jsx`:

     ```jsx
     import { useAuth } from "./AuthContext.jsx";
     import { loginApi } from "./api.js";
     import { useState } from "react";
     import { useNavigate } from "react-router-dom";

     function LoginPage() {
       const { login } = useAuth();
       const [email, setEmail] = useState("");
       const [password, setPassword] = useState("");
       const [error, setError] = useState("");
       const navigate = useNavigate();

       async function handleSubmit(e) {
         e.preventDefault();
         setError("");
         try {
           const { token, user } = await loginApi({ email, password });
           login(token, user);
           navigate("/dashboard");
         } catch (err) {
           setError(err.message);
         }
       }

       return (
         <div>
           <h2>Login</h2>
           {error && <p style={{ color: "red" }}>{error}</p>}
           <form onSubmit={handleSubmit}>
             <input
               type="email"
               placeholder="Email"
               value={email}
               onChange={(e) => setEmail(e.target.value)}
             />
             <input
               type="password"
               placeholder="Password"
               value={password}
               onChange={(e) => setPassword(e.target.value)}
             />
             <button type="submit">Login</button>
           </form>
         </div>
       );
     }
     ```

   - Use `useAuth().user` for `isLoggedIn` logic in `ProtectedRoute`.

### Practice Exercises
- Implement `RegisterPage` using `registerApi` and show “Registered, now log in” message on success.
- Show the logged‑in user’s email in a header/nav using `useAuth()`.

### Reflection & Self‑Check
In `notes-week9-day6.md`:
- How does Context help avoid “prop drilling” for auth state?
- What happens in your app if the stored token is invalid when the app loads?

### Optional Stretch
- Add a “Logout” button in a top nav bar that calls `logout()` from the context and navigates to `/login`.

---

## Day 7 – React Notes/Tasks Dashboard & Review

### Goals
- Build a **Dashboard** page that:
  - Fetches notes from your backend with `fetchNotesApi()`.
  - Displays them using React components.
  - Lets you add a new note via `createNoteApi()`.
- Review your understanding of React + API + Auth.

### Concepts to Reinforce
- Combining `useEffect`, context, and API calls.
- Handling loading and error states in a “real” screen.
- Component breakdown and reuse.

### Step‑by‑Step Tasks

1. **Create `DashboardPage.jsx`**
   - In `src/DashboardPage.jsx`:

     ```jsx
     import { useEffect, useState } from "react";
     import { fetchNotesApi, createNoteApi } from "./api.js";
     import { useAuth } from "./AuthContext.jsx";

     function DashboardPage() {
       const { user, logout } = useAuth();
       const [notes, setNotes] = useState([]);
       const [loading, setLoading] = useState(false);
       const [error, setError] = useState("");
       const [title, setTitle] = useState("");
       const [content, setContent] = useState("");

       async function loadNotes() {
         setLoading(true);
         setError("");
         try {
           const data = await fetchNotesApi();
           setNotes(data);
         } catch (err) {
           setError(err.message);
         } finally {
           setLoading(false);
         }
       }

       useEffect(() => {
         loadNotes();
       }, []);

       async function handleAddNote(e) {
         e.preventDefault();
         const trimmedTitle = title.trim();
         const trimmedContent = content.trim();
         if (!trimmedTitle || !trimmedContent) return;
         try {
           await createNoteApi({ title: trimmedTitle, content: trimmedContent });
           setTitle("");
           setContent("");
           loadNotes();
         } catch (err) {
           setError(err.message);
         }
       }

       return (
         <div>
           <div style={{ display: "flex", justifyContent: "space-between" }}>
             <h2>Dashboard</h2>
             <div>
               <span>Logged in as {user?.email}</span>
               <button onClick={logout} style={{ marginLeft: "8px" }}>
                 Logout
               </button>
             </div>
           </div>

           {loading && <p>Loading notes...</p>}
           {error && <p style={{ color: "red" }}>Error: {error}</p>}

           <form onSubmit={handleAddNote}>
             <input
               type="text"
               placeholder="Title"
               value={title}
               onChange={(e) => setTitle(e.target.value)}
             />
             <textarea
               placeholder="Content"
               value={content}
               onChange={(e) => setContent(e.target.value)}
             />
             <button type="submit">Add Note</button>
           </form>

           <ul>
             {notes.map((note) => (
               <li key={note._id}>
                 <strong>{note.title}</strong>
                 <p>{note.content}</p>
               </li>
             ))}
           </ul>
         </div>
       );
     }

     export default DashboardPage;
     ```

2. **Wire `DashboardPage` into routes**
   - In `App.jsx`:

     ```jsx
     import DashboardPage from "./DashboardPage.jsx";
     import { useAuth } from "./AuthContext.jsx";

     function App() {
       const { user, loading } = useAuth();

       if (loading) {
         return <p>Loading auth...</p>;
       }

       const isLoggedIn = !!user;

       return (
         <div>
           {/* nav, routes, etc. */}
           <Routes>
             {/* ...other routes... */}
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
     ```

3. **Test end‑to‑end**
   - Start backend (`npm run dev`) and frontend (`npm run dev`).
   - Register → Login in React app.
   - Visit `/dashboard`:
     - Should load only your notes from the backend.
     - Adding a note should persist in DB.
   - Verify in Postman/Atlas that created notes are tied to your user.

### Practice Exercises
- Add a “Delete” button next to each note in `DashboardPage`:
  - Create `deleteNoteApi` in `api.js`.
  - Call it on button click, then reload notes.
- Display a message when there are no notes yet.

### Reflection & Self‑Check (end of Week 9)
In `notes-week9-day7.md`, answer:
- From typing a note title and clicking “Add Note” in React, list the steps (React → fetch → Express → Mongo → back to React).
- Which parts of React feel natural now (components, state, effects)? Which still feel confusing?
- If you had to create a new React page that shows “My Profile” using `/api/auth/me`, could you outline the steps?

### Optional Stretch
- Add client‑side search or filter to `DashboardPage` (just like you did earlier in vanilla JS).
- Sketch (in notes) what this React app would look like if you replaced notes with tasks (title, description, status, dueDate) – this will align with your full Task Manager project.

---

By the end of Week 9 you should be able to:

- Set up and run a React app with Vite.
- Build React UIs using **components, props, state, and `useEffect`**.
- Use **React Router** for multiple pages and basic protected routes.
- Implement **real authentication** in React using your existing Node/Express/Mongo JWT backend.
- Build a React dashboard that **fetches, displays, and creates** user‑specific data.

This positions you to expand your app (Week 10) with more complex state management, better UX, and eventually a fully‑fledged authenticated Task Manager.
