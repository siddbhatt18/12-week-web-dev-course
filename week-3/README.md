### Week 3 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 3:**  
Learn **core JavaScript fundamentals (language only)**: variables, types, conditionals, functions, arrays, objects, and loops. No DOM yet – just pure logic. This is the foundation for everything you’ll do later.

Assume ~1.5–3 hours/day. If you have more time, put it into the practice/optional parts.

You’ll work mostly in:
- A new folder: `week3-js-basics`
- Plain `.js` files run either:
  - In the browser via `<script src="...">`, or  
  - With Node: `node filename.js` (easier for quick runs)

Use whichever feels more comfortable. I’ll describe with Node, but you can adapt.

---

## Day 1 – JavaScript Setup, Variables & Basic Types

### Goals
- Set up a place to run JS code.
- Understand variables and basic data types.
- Use `console.log` and read error messages.

### Concepts to Learn
- How to run JavaScript:
  - Node (`node file.js`) OR browser console.
- Variables:
  - `let`, `const`, (see `var` but don’t use it).
- Primitive types:
  - `number`, `string`, `boolean`, `null`, `undefined`.
- Basic operators:
  - `+`, `-`, `*`, `/`, `%`.
  - String concatenation.

### Step‑by‑Step Tasks

1. **Set up folder and first JS file**
   - Create a folder: `week3-js-basics`.
   - Inside, create `day1-basics.js`.
   - Open it in VS Code.

2. **Hello, JavaScript**
   - In `day1-basics.js`:

     ```js
     console.log("Hello from JavaScript!");
     ```

   - Run in terminal:

     ```bash
     node day1-basics.js
     ```

   - Confirm you see the message.

3. **Declare variables with basic types**
   - Add to `day1-basics.js`:

     ```js
     let age = 25;
     const name = "Your Name";
     let isLearningJS = true;
     let favoriteColor = null;
     let futureJob; // undefined

     console.log("Age:", age);
     console.log("Name:", name);
     console.log("Learning JS:", isLearningJS);
     console.log("Favorite color:", favoriteColor);
     console.log("Future job:", futureJob);
     ```

   - Run and inspect output.

4. **Play with operators**
   - Append:

     ```js
     let a = 10;
     let b = 3;

     console.log("a + b =", a + b);
     console.log("a - b =", a - b);
     console.log("a * b =", a * b);
     console.log("a / b =", a / b);
     console.log("a % b =", a % b);

     let greeting = "Hello, " + name + "!";
     console.log(greeting);
     ```

### Practice Exercises
In `day1-practice.js`:

1. Create variables:
   - `city`, `country`, `yearOfBirth`, `likesChocolate` (boolean).
2. Compute:
   - `currentYear` (just hard‑code, e.g., `2025`).
   - `age` from `currentYear - yearOfBirth`.
3. `console.log` a sentence using these variables, e.g.:  
   `"I am X years old, I live in CITY, COUNTRY, and it is true/false that I like chocolate."`

Run with `node day1-practice.js`.

### Reflection & Self‑Check
Create `notes-week3-day1.md` and answer:
- What is the difference between `let` and `const` in your own words?
- What’s the difference between `null` and `undefined`?

### Optional Stretch
- Try to reassign a `const` variable and see the error message.  
  Paste the error text into your notes and explain what it’s telling you.

---

## Day 2 – Comparisons, Boolean Logic & Conditionals

### Goals
- Compare values with comparison operators.
- Understand boolean logic (`&&`, `||`, `!`).
- Write `if`, `else if`, `else` statements.

### Concepts to Learn
- Comparison operators:
  - `>`, `<`, `>=`, `<=`.
  - `===`, `!==` (prefer these over `==`/`!=`).
- Boolean operators:
  - AND `&&`, OR `||`, NOT `!`.
- Conditionals:
  - `if (condition) { ... } else { ... }`.

### Step‑by‑Step Tasks

1. **Create `day2-conditionals.js`**
   - Start with some comparisons:

     ```js
     let x = 10;
     let y = 5;

     console.log("x > y:", x > y);
     console.log("x < y:", x < y);
     console.log("x === y:", x === y);
     console.log("x !== y:", x !== y);
     ```

2. **Booleans and conditions**
   - Add:

     ```js
     let isStudent = true;
     let hasJob = false;

     console.log("isStudent && hasJob:", isStudent && hasJob);
     console.log("isStudent || hasJob:", isStudent || hasJob);
     console.log("!isStudent:", !isStudent);
     ```

3. **Simple `if/else`**
   - Add:

     ```js
     let temperature = 30;

     if (temperature > 25) {
       console.log("It's warm outside.");
     } else {
       console.log("It's not that warm.");
     }
     ```

4. **`if / else if / else` chain**
   - Add:

     ```js
     let score = 78;

     if (score >= 90) {
       console.log("Grade: A");
     } else if (score >= 80) {
       console.log("Grade: B");
     } else if (score >= 70) {
       console.log("Grade: C");
     } else {
       console.log("Grade: D or below");
     }
     ```

   - Change `score` and re‑run to see different branches.

### Practice Exercises
In `day2-practice.js`:

1. Write a program that:
   - Has a variable `age`.
   - Logs:
     - `"You are a minor"` if age < 18.
     - `"You are an adult"` if 18–64.
     - `"You are a senior"` if 65+.

2. Add a `country` variable.
   - If `country === "USA"` AND `age >= 16`, log `"You can drive in the USA"`.
   - Else log `"You might not meet driving requirements in the USA"`.

3. Try changing `age` and `country` and re‑run.

### Reflection & Self‑Check
In `notes-week3-day2.md`:
- Why is `===` generally safer than `==`?
- Think of one real‑world rule (e.g., “eligible for student discount”) and write it as:
  - A sentence in plain English.
  - A boolean expression using `&&` and `||`.

### Optional Stretch
- Write a “password check”:
  - `password` variable.
  - If length < 6 → `"Password too short"`.
  - Else if length < 10 → `"Password could be stronger"`.
  - Else → `"Password length is good"`.  
  (Use `password.length` and hard‑code a test string.)

---

## Day 3 – Functions: Reuse & Abstraction

### Goals
- Understand what functions are and why we use them.
- Write and call functions with parameters and return values.
- Differentiate between function declarations and arrow functions.

### Concepts to Learn
- Function declaration:

  ```js
  function add(a, b) {
    return a + b;
  }
  ```

- Parameters vs arguments.
- Return values vs `console.log`.
- Arrow functions:

  ```js
  const multiply = (a, b) => {
    return a * b;
  };
  ```

### Step‑by‑Step Tasks

1. **Create `day3-functions.js`**
   - Start with a simple function:

     ```js
     function greet(name) {
       console.log("Hello, " + name + "!");
     }

     greet("Alice");
     greet("Bob");
     ```

2. **Return values**
   - Add:

     ```js
     function add(a, b) {
       return a + b;
     }

     const sum = add(3, 7);
     console.log("sum:", sum);
     console.log("add(10, 5):", add(10, 5));
     ```

   - Note: `return` gives back a value that you can store or use.

3. **Practice: function with logic inside**
   - Add:

     ```js
     function categorizeAge(age) {
       if (age < 18) {
         return "minor";
       } else if (age < 65) {
         return "adult";
       } else {
         return "senior";
       }
     }

     console.log(categorizeAge(15));
     console.log(categorizeAge(30));
     console.log(categorizeAge(80));
     ```

4. **Arrow functions**
   - Convert `add` into arrow form:

     ```js
     const addArrow = (a, b) => {
       return a + b;
     };

     console.log("addArrow(2, 3):", addArrow(2, 3));
     ```

   - For a very simple function, use implicit return:

     ```js
     const square = (x) => x * x;
     console.log("square(4):", square(4));
     ```

### Practice Exercises
In `day3-practice.js`:

1. Write `convertCelsiusToFahrenheit(celsius)`:
   - Formula: `fahrenheit = celsius * 9/5 + 32`.
   - Test with a few values: 0, 20, 100.

2. Write `isAdult(age)`:
   - Return `true` if age >= 18, else `false`.

3. Write `greetUser(name, isMorning)`:
   - If `isMorning` is true → return `"Good morning, NAME!"`.
   - Else → `"Hello, NAME!"`.

### Reflection & Self‑Check
In `notes-week3-day3.md`:
- Why is returning a value more flexible than just logging inside the function?
- Think of 2 tasks you did yesterday in code that could be inside functions.

### Optional Stretch
- Write a function `max(a, b, c)` that returns the largest of the three numbers (without using built‑in `Math.max`).
- Before coding, write pseudo‑code:
  - “If a is bigger than b and c, return a; else if …” etc.

---

## Day 4 – Arrays: Lists of Data & Basic Methods

### Goals
- Understand arrays as ordered lists.
- Access, add, remove items.
- Use `for` loops and array methods like `push`, `pop`, `length`.

### Concepts to Learn
- Creating arrays:

  ```js
  const numbers = [10, 20, 30];
  ```

- Accessing items: `numbers[0]`, `numbers[1]`.
- Length: `numbers.length`.
- Common methods:
  - `push`, `pop`, `shift`, `unshift`.

### Step‑by‑Step Tasks

1. **Create `day4-arrays.js`**
   - Start with a simple array:

     ```js
     const fruits = ["apple", "banana", "orange"];
     console.log("fruits:", fruits);
     console.log("first fruit:", fruits[0]);
     console.log("second fruit:", fruits[1]);
     console.log("number of fruits:", fruits.length);
     ```

2. **Modify array contents**
   - Add:

     ```js
     fruits.push("grape");
     console.log("after push:", fruits);

     const lastFruit = fruits.pop();
     console.log("popped:", lastFruit);
     console.log("after pop:", fruits);

     fruits.unshift("mango");
     console.log("after unshift:", fruits);

     const firstFruit = fruits.shift();
     console.log("shifted:", firstFruit);
     console.log("after shift:", fruits);
     ```

3. **Loop through an array (classic `for` loop)**
   - Add:

     ```js
     const numbers = [1, 2, 3, 4, 5];
     let sum = 0;

     for (let i = 0; i < numbers.length; i++) {
       sum = sum + numbers[i];
     }

     console.log("sum of numbers:", sum);
     ```

4. **Basic array problem**
   - Write a function in the same file:

     ```js
     function findAverage(nums) {
       let total = 0;
       for (let i = 0; i < nums.length; i++) {
         total += nums[i];
       }
       return total / nums.length;
     }

     console.log("average:", findAverage([10, 20, 30]));
     ```

### Practice Exercises
In `day4-practice.js`:

1. Create an array `countries` with at least 5 country names.
   - Log each country with its index:  
     `"0: CountryName"`, `"1: CountryName"`, etc.

2. Write `countLongNames(namesArray)`:
   - Return how many names have length > 5.
   - Test with a few input arrays.

### Reflection & Self‑Check
In `notes-week3-day4.md`:
- What does `array.length` represent?
- What happens if you access `array[100]` on a small array?

### Optional Stretch
- Write `maxInArray(numbers)` that returns the largest number in an array without using `Math.max`:
  - Start with assuming the first element is the max.
  - Loop and compare.

---

## Day 5 – Objects: Key–Value Pairs & Basic Modeling

### Goals
- Understand objects as collections of related data.
- Access properties with dot and bracket notation.
- Combine objects with arrays (e.g., list of users).

### Concepts to Learn
- Creating an object:

  ```js
  const person = {
    name: "Alice",
    age: 25,
  };
  ```

- Access/update properties:
  - `person.name`, `person["age"]`.
- Nested objects / arrays inside objects.

### Step‑by‑Step Tasks

1. **Create `day5-objects.js`**
   - Define a `person`:

     ```js
     const person = {
       name: "John Doe",
       age: 30,
       city: "New York",
       isStudent: false,
     };

     console.log("person:", person);
     console.log("name:", person.name);
     console.log("age:", person["age"]);
     ```

2. **Update and add properties**
   - Add:

     ```js
     person.age = 31;
     person.job = "Developer";

     console.log("updated person:", person);
     ```

3. **Object with array property**
   - Add:

     ```js
     const student = {
       name: "Sara",
       age: 22,
       courses: ["Math", "Computer Science", "Art"],
     };

     console.log(student.name + " is taking " + student.courses.length + " courses.");
     console.log("First course:", student.courses[0]);
     ```

4. **Array of objects**
   - Add:

     ```js
     const users = [
       { name: "Alice", age: 25 },
       { name: "Bob", age: 32 },
       { name: "Charlie", age: 28 },
     ];

     console.log("users:", users);
     console.log("Second user name:", users[1].name);
     ```

### Practice Exercises
In `day5-practice.js`:

1. Create an object `book` with properties:
   - `title`, `author`, `year`, `isRead`.
2. Log a sentence using the object:
   - `"I am reading TITLE by AUTHOR."`
3. Create an array `library` containing 3 book objects.
   - Write a loop that logs only the titles.

### Reflection & Self‑Check
In `notes-week3-day5.md`:
- In your own words, how are objects different from arrays?
- For representing a person’s profile, why might an object be more suitable than an array?

### Optional Stretch
- Make a function `describePerson(personObj)`:
  - Takes an object like `person` and returns a sentence summarizing them.
- Call it for several different person objects.

---

## Day 6 – Combining Arrays, Objects & Functions (Mini Problems)

### Goals
- Practice combining everything: arrays, objects, loops, functions, conditionals.
- Start thinking more algorithmically (step‑by‑step problem solving).
- Learn a simple debugging routine.

### Concepts to Reinforce
- Data modeling with arrays of objects.
- Writing helper functions to keep code clean.
- Debugging:
  - Add `console.log` at key steps.
  - Check arguments and intermediate results.

### Step‑by‑Step Tasks

1. **Create `day6-combine.js`**
   - Define a small “todo list” in memory:

     ```js
     const todos = [
       { id: 1, text: "Learn JavaScript basics", completed: false },
       { id: 2, text: "Practice CSS Flexbox", completed: true },
       { id: 3, text: "Build my portfolio", completed: false },
     ];
     ```

2. **Function: printTodos**
   - Add:

     ```js
     function printTodos(list) {
       console.log("My Todos:");
       for (let i = 0; i < list.length; i++) {
         const todo = list[i];
         const status = todo.completed ? "[x]" : "[ ]";
         console.log(status, todo.id + ":", todo.text);
       }
     }

     printTodos(todos);
     ```

3. **Function: addTodo**
   - Add:

     ```js
     function addTodo(list, text) {
       const newId = list.length === 0 ? 1 : list[list.length - 1].id + 1;
       const newTodo = { id: newId, text: text, completed: false };
       list.push(newTodo);
     }

     addTodo(todos, "Review JavaScript functions");
     printTodos(todos);
     ```

4. **Function: markTodoCompleted**
   - Add:

     ```js
     function markTodoCompleted(list, id) {
       for (let i = 0; i < list.length; i++) {
         if (list[i].id === id) {
           list[i].completed = true;
           return; // stop after finding
         }
       }
       console.log("Todo with id", id, "not found.");
     }

     markTodoCompleted(todos, 3);
     printTodos(todos);
     ```

### Practice Exercises
In `day6-practice.js`:

1. Recreate the `todos` logic:
   - Implement `printTodos`, `addTodo`, `markTodoCompleted`.
2. Add `deleteTodo(list, id)`:
   - Remove the todo with matching `id`.
   - (Hint: use a new array and push all except the one to be deleted, or use `splice` if you find it.)

### Debugging Habit
If something doesn’t work:
- Add `console.log` statements:
  - At the start of your function: log the inputs.
  - Inside loops: log current item / index.
  - Before return: log what you’re returning or changing.
- When reading an error, copy the exact line mentioned and check what variable might be `undefined` there.

### Reflection & Self‑Check
In `notes-week3-day6.md`:
- Describe in words how `markTodoCompleted` works.
- What was the hardest bug you hit today? How did you fix it?

### Optional Stretch
- Add `getCompletedTodos(list)` that returns a new array with only completed todos.
- Add `searchTodos(list, searchText)` that returns todos whose `text` includes the search text (case‑insensitive).

---

## Day 7 – Review & Mini JavaScript “Capstone” (No DOM Yet)

### Goals
- Review all Week 3 concepts.
- Build a small, self‑contained JS “module” that manipulates data.
- Practice designing and testing your own functions.

### Concepts to Reinforce
- Variables, types, conditionals.
- Functions with parameters/returns.
- Arrays and objects together.
- Thinking in terms of input → processing → output.

### Step‑by‑Step Tasks

1. **Quick review quiz (write in `notes-week3-day7.md` before coding)**
   - Define these in your own words (1–2 lines each):
     - Variable
     - Function
     - Array
     - Object
     - Loop
   - Write a simple example of each (just in the notes, not for execution).

2. **Design a small text‑based “student grade manager”**
   - New file: `day7-mini-project.js`.
   - Data structure (example):

     ```js
     const students = [
       { name: "Alice", scores: [80, 90, 85] },
       { name: "Bob", scores: [70, 75, 72] },
       { name: "Charlie", scores: [95, 92, 88] },
     ];
     ```

3. **Plan functions (write in comments first)**
   - In `day7-mini-project.js`, write comments for what functions you’ll have, for example:

     ```js
     // 1. calculateAverage(scoresArray) -> returns average score
     // 2. getStudentAverage(studentsArray, studentName) -> returns average of that student
     // 3. getClassAverage(studentsArray) -> average of all students' averages
     // 4. printReport(studentsArray) -> logs each student's average and the class average
     ```

   - Think for 5–10 minutes about how to implement each. Don’t rush.

4. **Implement `calculateAverage(scoresArray)`**
   - Use a loop like you did earlier:

     ```js
     function calculateAverage(scores) {
       let total = 0;
       for (let i = 0; i < scores.length; i++) {
         total += scores[i];
       }
       return total / scores.length;
     }

     console.log("Test average:", calculateAverage([80, 90, 100]));
     ```

5. **Implement `getStudentAverage(studentsArray, studentName)`**
   - Find the student object by `name`, then use `calculateAverage` on its `scores`.
   - Handle “not found” with a message or `null`.

6. **Implement `getClassAverage(studentsArray)`**
   - For each student:
     - Compute their average.
     - Add to a running total.
   - At the end, divide total by number of students.

7. **Implement `printReport(studentsArray)`**
   - Loop over students:
     - Log `"Alice: 85"`, `"Bob: 72"`, etc.
   - Then log `"Class average: X"`.

8. **Run and test**
   - Try adding new students with different scores.
   - Does your code adapt?

### Practice Exercises
- Add a function `addStudent(studentsArray, name, scoresArray)`:
  - Push a new student object.
- Add a function `getTopStudent(studentsArray)`:
  - Returns the name of the student with highest average.

### Reflection & Self‑Check
In `notes-week3-day7.md`, add:
- Which part of the mini‑project required the most thinking?
- If you had another day, what would you add (e.g., grading letters A/B/C, sorting students by score)?

### Optional Stretch
- Create a second mini‑project idea (just plan, don’t fully code yet), e.g.:
  - Expense tracker with `expenses` array of objects.
  - Habit tracker with `habits` array of objects.
- Write data structure + function ideas in a new file `ideas-week3.js` as comments:
  - This primes you for upcoming DOM projects.

---

### After Week 3

By the end of this week, you should:
- Be comfortable writing small functions and using arrays/objects.
- Understand how to model simple “real‑world” data in JS.
- Be able to break a problem down into several functions and implement them step by step.

Next, you’ll bring this JS knowledge into the browser: manipulating the DOM and making interactive pages. If you’d like, I can generate a similar detailed daily plan for Week 4 focused on DOM + events + building the interactive to‑do list.
