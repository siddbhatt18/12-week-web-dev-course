### Week 4 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 4:**  
Bring your JavaScript into the browser: **DOM manipulation, events, forms, and localStorage**. By the end of the week you’ll have a working **interactive To‑Do List app (frontend only)**.

Assume ~1.5–3 hours/day. Use extra time on practice/optional sections.

You’ll mainly work in a new project folder:

- `week4-dom-todo/`
  - `index.html`
  - `styles.css`
  - `app.js`

---

## Day 1 – DOM Basics & Project Setup

### Goals
- Set up a simple page with linked JS and CSS.
- Understand what the DOM is and how to access elements.
- Practice logging and inspecting DOM nodes.

### Concepts to Learn
- DOM = Document Object Model: browser’s in‑memory representation of HTML.
- How to:
  - Include `<script src="app.js">` at the end of `<body>`.
  - Use `document.querySelector` and `document.querySelectorAll`.
- Using console + DevTools to inspect elements.

### Step‑by‑Step Tasks

1. **Create project structure**
   - Create folder `week4-dom-todo`.
   - Inside, create:
     - `index.html`
     - `styles.css`
     - `app.js`
   - Open the folder in VS Code.

2. **Basic HTML skeleton**
   - In `index.html`:

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1.0" />
       <title>Week 4 – DOM Practice</title>
       <link rel="stylesheet" href="styles.css" />
     </head>
     <body>
       <h1>DOM Practice Page</h1>

       <p class="intro">This is a paragraph we will manipulate with JavaScript.</p>

       <ul id="example-list">
         <li class="item">Item 1</li>
         <li class="item">Item 2</li>
         <li class="item">Item 3</li>
       </ul>

       <script src="app.js"></script>
     </body>
     </html>
     ```

3. **Basic CSS (just to differentiate things visually)**
   - In `styles.css`:

     ```css
     body {
       font-family: Arial, sans-serif;
       margin: 20px;
     }

     .intro {
       color: #555;
     }

     .item {
       padding: 4px;
       border-bottom: 1px solid #ddd;
     }
     ```

4. **Connect JS and log elements**
   - In `app.js`:

     ```js
     console.log("JS is connected!");

     const heading = document.querySelector("h1");
     const introPara = document.querySelector(".intro");
     const list = document.querySelector("#example-list");
     const items = document.querySelectorAll(".item");

     console.log("heading:", heading);
     console.log("introPara:", introPara);
     console.log("list:", list);
     console.log("items NodeList:", items);
     ```

   - Open `index.html` in the browser (Live Server or double‑click).
   - Open DevTools Console and verify logs.

### Practice Exercises
- Select and log:
  - The first list item: `document.querySelector(".item")`.
  - The third item: use `querySelectorAll(".item")` and index `[2]`.
- Change the `<title>` to “Week 4 – DOM Basics” and confirm in the tab.

### Reflection & Self‑Check
Create `notes-week4-day1.md`:
- In your own words:
  - What is the DOM?
  - How is it related to but different from the HTML file?

### Optional Stretch
- Add another section in HTML with a few paragraphs and list items.
- In `app.js`, practice selecting only elements inside that section using more specific selectors like `section p`, `section ul li`.

---

## Day 2 – Reading & Changing DOM Content and Styles

### Goals
- Read and modify text and HTML of elements.
- Add/remove/change CSS classes with JS.
- Understand the difference between `textContent` and `innerHTML`.

### Concepts to Learn
- `element.textContent` vs `element.innerHTML`.
- `element.classList.add/remove/toggle`.
- Basic inline styles: `element.style.property = value`.

### Step‑by‑Step Tasks

1. **Modify text content**
   - In `app.js`, after your existing code:

     ```js
     heading.textContent = "Updated with JavaScript!";
     introPara.textContent = "This text was changed using JS.";
     ```

   - Refresh the page and see the changes.

2. **Experiment with `innerHTML`**
   - Add:

     ```js
     list.innerHTML = `
       <li class="item">New item A</li>
       <li class="item">New item B</li>
       <li class="item">New item C</li>
     `;
     ```

   - Observe that it replaced the previous `<li>` elements.

   - In `notes-week4-day2.md`, note:
     - `innerHTML` replaces the whole inner structure.
     - It can be dangerous if used with user input (XSS risk – just note conceptually).

3. **Change styles via JS**
   - Add:

     ```js
     heading.style.color = "#2980b9";
     heading.style.textTransform = "uppercase";
     ```

4. **Use `classList`**
   - In `styles.css`, add:

     ```css
     .highlight {
       background-color: #ffeaa7;
     }
     ```

   - In `app.js`:

     ```js
     const firstItem = document.querySelector(".item");
     firstItem.classList.add("highlight");
     ```

   - See first item highlighted.

5. **Toggle a class**
   - Add later:

     ```js
     firstItem.classList.toggle("highlight"); // removes
     firstItem.classList.toggle("highlight"); // adds back
     ```

   - Watch in DevTools how the class list changes.

### Practice Exercises
Create a new section in HTML:

```html
<section id="practice">
  <h2>Practice Section</h2>
  <p class="practice-text">Original text</p>
</section>
```

In JS:

- Select `.practice-text`.
- Change its text to include your name.
- Add a class `.emphasis` (define in CSS with bold, color, etc.).

### Reflection & Self‑Check
In `notes-week4-day2.md`:
- When would you use `textContent` vs `innerHTML`?
- Why is `classList` usually better than directly changing `style` from JS?

### Optional Stretch
- Create a function `highlightAllItems()` that:
  - Selects all `.item` elements.
  - Adds the `highlight` class to each.

Call it from your code and verify.

---

## Day 3 – Events & Basic Interactivity

### Goals
- React to user actions: clicks, form submissions, keyup.
- Understand `addEventListener`.
- Use `event.preventDefault()` to control form behavior.

### Concepts to Learn
- `element.addEventListener("click", handler)`.
- Event object: `event`.
- Form submit events and preventing default page reload.

### Step‑by‑Step Tasks

1. **Add a button in HTML**
   - In `index.html`, under the heading:

     ```html
     <button id="change-color-btn">Change Heading Color</button>
     ```

2. **Handle click event in JS**
   - In `app.js`:

     ```js
     const colorButton = document.querySelector("#change-color-btn");

     colorButton.addEventListener("click", () => {
       // Random color
       const randomColor = "#" + Math.floor(Math.random() * 16777215).toString(16);
       heading.style.color = randomColor;
       console.log("Changed heading color to", randomColor);
     });
     ```

3. **Add a simple form**
   - In `index.html`, below the list:

     ```html
     <h2>Message Form</h2>
     <form id="message-form">
       <label for="message-input">Enter a message:</label>
       <input id="message-input" type="text" />
       <button type="submit">Show Message</button>
     </form>

     <p id="message-output"></p>
     ```

4. **Handle form submit**
   - In `app.js`:

     ```js
     const messageForm = document.querySelector("#message-form");
     const messageInput = document.querySelector("#message-input");
     const messageOutput = document.querySelector("#message-output");

     messageForm.addEventListener("submit", (event) => {
       event.preventDefault(); // stop page reload

       const text = messageInput.value.trim();
       if (text === "") {
         messageOutput.textContent = "Please enter a message.";
         messageOutput.style.color = "red";
         return;
       }

       messageOutput.textContent = "You wrote: " + text;
       messageOutput.style.color = "green";

       messageInput.value = "";
     });
     ```

5. **Try keyup event (optional but useful)**
   - Add:

     ```js
     messageInput.addEventListener("keyup", () => {
       console.log("Current value:", messageInput.value);
     });
     ```

### Practice Exercises
- Add another button that toggles the visibility of the `.intro` paragraph:
  - Use `classList.toggle("hidden")` and define `.hidden { display: none; }` in CSS.
- Add a counter that tracks how many times the “Change Heading Color” button was clicked and display it next to the button.

### Reflection & Self‑Check
In `notes-week4-day3.md`:
- What does `event.preventDefault()` do, and why is it important for forms?
- Why is it useful to `trim()` the input value?

### Optional Stretch
- In the form handler, if message length is > 50, show a warning (e.g., “Message is quite long”) but still accept it.
- Log both the original value and the trimmed value to understand the difference.

---

## Day 4 – To‑Do App: Structure & Rendering (Data → DOM)

### Goals
- Start building your To‑Do List app.
- Design a simple data structure for todos.
- Render an array of todos into the DOM.

### Concepts to Learn
- Data modeling: representing todos as objects.
- Separating data from DOM rendering.
- Basic one‑way data flow: data → render function → DOM.

### Step‑by‑Step Tasks

1. **Set up a new HTML structure for the To‑Do App**
   - In `index.html`, replace the practice content (or add below) with:

     ```html
     <main class="todo-app">
       <h1>My To‑Do List</h1>

       <form id="todo-form">
         <input
           id="todo-input"
           type="text"
           placeholder="Add a new task..."
         />
         <button type="submit">Add</button>
       </form>

       <ul id="todo-list"></ul>
     </main>
     ```

2. **Basic styles for the app**
   - In `styles.css` (simplified):

     ```css
     .todo-app {
       max-width: 500px;
       margin: 0 auto;
       padding: 20px;
       border: 1px solid #ddd;
       border-radius: 8px;
       background-color: #fff;
     }

     #todo-form {
       display: flex;
       gap: 10px;
       margin-bottom: 15px;
     }

     #todo-input {
       flex: 1;
       padding: 8px;
     }

     #todo-list {
       list-style: none;
       padding: 0;
       margin: 0;
     }

     .todo-item {
       display: flex;
       justify-content: space-between;
       align-items: center;
       padding: 6px 0;
       border-bottom: 1px solid #eee;
     }

     .todo-text.completed {
       text-decoration: line-through;
       color: #888;
     }
     ```

3. **Model your todos in JS**
   - In `app.js`, start fresh or below existing code; for clarity, you can comment out the earlier practice and focus here:

     ```js
     const todos = [
       { id: 1, text: "Learn JavaScript DOM", completed: false },
       { id: 2, text: "Build a To‑Do app", completed: false },
     ];

     const todoForm = document.querySelector("#todo-form");
     const todoInput = document.querySelector("#todo-input");
     const todoList = document.querySelector("#todo-list");
     ```

4. **Render function**
   - Add:

     ```js
     function renderTodos(list) {
       // Clear existing content
       todoList.innerHTML = "";

       // For each todo, create an li
       list.forEach((todo) => {
         const li = document.createElement("li");
         li.className = "todo-item";

         const span = document.createElement("span");
         span.className = "todo-text";
         span.textContent = todo.text;

         // Later we'll add buttons & completion logic
         li.appendChild(span);

         todoList.appendChild(li);
       });
     }

     renderTodos(todos);
     ```

   - Refresh and confirm you see the initial todos.

### Practice Exercises
- Add a third todo to the initial `todos` array and confirm it shows.
- Add an index number before each task (e.g., “1. Learn JS DOM”), either in the data or in the render function.

### Reflection & Self‑Check
In `notes-week4-day4.md`:
- Why is it useful to have a separate `renderTodos` function instead of building list items all over the place?
- What happens if you forget to clear `todoList.innerHTML` before rendering?

### Optional Stretch
- Adjust `renderTodos` to show `(completed)` next to completed todos (for now you can manually set one todo’s `completed` to `true`).

---

## Day 5 – To‑Do App: Adding, Toggling & Deleting Todos

### Goals
- Add new todos from user input.
- Toggle a todo’s `completed` state.
- Delete a todo from the list.
- Introduce event delegation for list interactions.

### Concepts to Learn
- Handling submit to add todos.
- Updating your `todos` array, then re‑rendering.
- Event delegation: attach one listener to the list, not each item individually.
- `data-*` attributes for storing ids in DOM.

### Step‑by‑Step Tasks

1. **Add new todos via the form**
   - In `app.js`:

     ```js
     todoForm.addEventListener("submit", (event) => {
       event.preventDefault();
       const text = todoInput.value.trim();
       if (text === "") return;

       const newTodo = {
         id: todos.length === 0 ? 1 : todos[todos.length - 1].id + 1,
         text,
         completed: false,
       };

       todos.push(newTodo);
       todoInput.value = "";
       renderTodos(todos);
     });
     ```

2. **Enhance `renderTodos` with buttons and ids**
   - Update `renderTodos`:

     ```js
     function renderTodos(list) {
       todoList.innerHTML = "";

       list.forEach((todo) => {
         const li = document.createElement("li");
         li.className = "todo-item";
         li.dataset.id = todo.id; // store id

         const span = document.createElement("span");
         span.className = "todo-text";
         if (todo.completed) {
           span.classList.add("completed");
         }
         span.textContent = todo.text;

         const buttonsContainer = document.createElement("div");

         const toggleBtn = document.createElement("button");
         toggleBtn.textContent = todo.completed ? "Undo" : "Done";
         toggleBtn.className = "toggle-btn";

         const deleteBtn = document.createElement("button");
         deleteBtn.textContent = "Delete";
         deleteBtn.className = "delete-btn";

         buttonsContainer.appendChild(toggleBtn);
         buttonsContainer.appendChild(deleteBtn);

         li.appendChild(span);
         li.appendChild(buttonsContainer);

         todoList.appendChild(li);
       });
     }
     ```

   - Update CSS to style buttons a bit if you like.

3. **Event delegation on the list**
   - Add to `app.js`:

     ```js
     todoList.addEventListener("click", (event) => {
       const target = event.target;
       const li = target.closest(".todo-item");
       if (!li) return; // clicked outside a todo item
       const id = Number(li.dataset.id);

       if (target.classList.contains("toggle-btn")) {
         toggleTodoCompleted(id);
       } else if (target.classList.contains("delete-btn")) {
         deleteTodo(id);
       }
     });

     function toggleTodoCompleted(id) {
       const todo = todos.find((t) => t.id === id);
       if (!todo) return;
       todo.completed = !todo.completed;
       renderTodos(todos);
     }

     function deleteTodo(id) {
       const index = todos.findIndex((t) => t.id === id);
       if (index === -1) return;
       todos.splice(index, 1);
       renderTodos(todos);
     }
     ```

4. **Test thoroughly**
   - Add tasks, mark them done/undo, delete some.
   - Try with multiple items and ensure the ids still work.

### Practice Exercises
- Add a counter above the list showing how many todos remain (e.g., “3 tasks remaining”).
  - Update it inside `renderTodos(todos)` based on `completed` values.

### Reflection & Self‑Check
In `notes-week4-day5.md`:
- Describe in words how event delegation works in your app.
- Why do we store the `id` on `li.dataset.id` instead of, say, using the todo text?

### Optional Stretch
- Allow editing a todo’s text:
  - When clicking on the text, prompt for a new value using `prompt()`.
  - Update the `todo.text` and re‑render.

---

## Day 6 – Persisting Todos with localStorage

### Goals
- Save todos to localStorage so they persist after page refresh.
- Load todos from localStorage on page load.
- Understand JSON serialization (`JSON.stringify` / `JSON.parse`).

### Concepts to Learn
- `localStorage.setItem(key, value)` and `localStorage.getItem(key)`.
- Only strings can be stored directly; objects/arrays need JSON.
- Handling cases where there’s no data yet (`null`).

### Step‑by‑Step Tasks

1. **Create helper functions for storage**
   - In `app.js`, near the top (before defining `todos` if possible):

     ```js
     const STORAGE_KEY = "my_todo_list";

     function saveTodos(list) {
       localStorage.setItem(STORAGE_KEY, JSON.stringify(list));
     }

     function loadTodos() {
       const stored = localStorage.getItem(STORAGE_KEY);
       if (!stored) {
         return [];
       }
       try {
         return JSON.parse(stored);
       } catch (error) {
         console.error("Error parsing todos from localStorage", error);
         return [];
       }
     }
     ```

2. **Initialize todos from localStorage**
   - Replace your `const todos = [...]` line with:

     ```js
     let todos = loadTodos();

     // If first time and empty, you can add an example item:
     if (todos.length === 0) {
       todos = [
         { id: 1, text: "Welcome to your To‑Do list!", completed: false },
       ];
     }
     ```

3. **Save after each change**
   - In functions that modify `todos` (`toggleTodoCompleted`, `deleteTodo`, form submit handler), after `renderTodos(todos)`, call `saveTodos(todos)`.

   - Example in `toggleTodoCompleted`:

     ```js
     function toggleTodoCompleted(id) {
       const todo = todos.find((t) => t.id === id);
       if (!todo) return;
       todo.completed = !todo.completed;
       renderTodos(todos);
       saveTodos(todos);
     }
     ```

   - Same pattern in `deleteTodo` and in the submit handler after `todos.push(newTodo)`.

4. **Test persistence**
   - Add a few todos.
   - Refresh the page.
   - Confirm they are still there.
   - Check DevTools → Application (or Storage) → localStorage to see the raw data.

### Practice Exercises
- After adding localStorage, try:
  - Deleting all todos, then refreshing.
  - Confirm that if `todos` is empty, your app still works (maybe you skip adding the “welcome” todo in that case).

### Reflection & Self‑Check
In `notes-week4-day6.md`:
- Why do we need `JSON.stringify` and `JSON.parse`?
- If localStorage only stores strings, what would happen if you tried to store an object directly?

### Optional Stretch
- Add a **“Clear completed”** button that:
  - Removes all completed todos from the array.
  - Saves and re‑renders.
- Add a **“Reset list”** button that:
  - Clears localStorage and sets todos back to a default state.

---

## Day 7 – Refactor, Polish & Mini Retrospective

### Goals
- Clean up your To‑Do code.
- Improve UX with small touches (validation messages, empty state).
- Reflect on your problem‑solving and understanding of DOM + events.

### Concepts to Reinforce
- Separation of concerns: data vs DOM vs event handlers.
- “Empty state” patterns (what to show when no todos).
- Small, well‑named functions vs huge event handlers.

### Step‑by‑Step Tasks

1. **Code organization pass**
   - Open `app.js`.
   - Group related parts logically:
     1. Constants / selectors / state (`STORAGE_KEY`, DOM elements, `todos`).
     2. Storage functions (`saveTodos`, `loadTodos`).
     3. Core logic functions (`renderTodos`, `toggleTodoCompleted`, `deleteTodo`, etc.).
     4. Event listeners setup (`todoForm.addEventListener`, `todoList.addEventListener`).
   - Add brief comments above each function explaining its purpose.

2. **Improve empty state UI**
   - Inside `renderTodos(list)`, after clearing `todoList.innerHTML`:

     ```js
     if (list.length === 0) {
       const emptyMessage = document.createElement("p");
       emptyMessage.textContent = "No tasks yet. Add your first one!";
       emptyMessage.className = "empty-message";
       todoList.appendChild(emptyMessage);
       return;
     }
     ```

   - Style `.empty-message` in CSS (lighter text, centered).

3. **Improve user feedback**
   - If a user tries to add an empty todo:
     - Maybe show a short message near the form instead of silently ignoring.
   - You can add a `<p id="form-error"></p>` under the form and set `textContent` accordingly.

4. **Test full flow**
   - Add, toggle, delete, clear completed (if you added it), refresh to test localStorage.
   - Try edge cases:
     - Very long todo text.
     - Many items.
     - Deleting the last item.

5. **Final git commit for Week 4**
   - If you haven’t already, `git init` this project and commit.
   - Ideally:
     ```bash
     git add .
     git commit -m "Complete basic todo app with DOM, events, and localStorage"
     ```

### Practice Exercises
- Rename at least one poorly named variable or function to something clearer.
- Move any repeated small code into a helper function (e.g., a function that finds a todo by id).

### Reflection & Self‑Check
Create `notes-week4-day7.md`:
- Answer:
  - Which part of building the To‑Do app felt hardest? (Adding, toggling, deleting, or saving?)
  - How does the data flow when you click “Done” on a task? Describe step by step.
  - If you had to explain event delegation to a friend, how would you do it?

### Optional Stretch
- Add a simple **filter**:
  - Three buttons: “All”, “Active”, “Completed”.
  - Keep a `currentFilter` variable (`"all" | "active" | "completed"`).
  - In `renderTodos`, filter the `todos` array based on `currentFilter` before creating DOM elements.

---

### After Week 4

By the end of this week you should be able to:

- Select and manipulate DOM elements with JS.
- React to user interactions via events.
- Keep your data in sync with the UI.
- Persist basic data using localStorage.
- Break a frontend feature into:
  - Data (array of objects)
  - Logic (functions)
  - UI (DOM & events)

Next up (Week 5–6) you’ll move into **Node.js + Express** and start building real server‑side APIs that your frontend apps can talk to.
