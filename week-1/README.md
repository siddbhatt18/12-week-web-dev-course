### Week 1 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 1:**  
Understand how the web works, set up your tools, and build simple but well‑structured HTML pages with a taste of CSS.

You can assume ~1.5–3 hours per day. If you have more time, deepen the practice sections.

---

## Day 1 – How the Web Works & Setting Up Your Environment

### Goals
- Understand at a high level how a website gets from a server to your browser.
- Set up your coding environment.
- Create and open your first HTML file.

### Concepts to Learn (read & think about)
- What happens when you type `https://example.com` in a browser?
  - Browser → HTTP request → server → HTTP response → browser renders page.
- Definitions:
  - Client vs server.
  - URL, HTTP, HTML, CSS, JavaScript (just high‑level today).
- Files and folders for a project.

### Step‑by‑Step Tasks

1. **Set up tools**
   - Install:
     - A modern browser (Chrome or Firefox).
     - VS Code (or another code editor, but this plan assumes VS Code).
   - Open VS Code and explore:
     - The file explorer (left).
     - The editor area (center).
     - The status bar (bottom).

2. **Create your first project folder**
   - Create a folder named `week1-html-basics` on your computer.
   - Open it in VS Code:
     - VS Code → File → Open Folder → select `week1-html-basics`.

3. **Create your first HTML file**
   - In VS Code, create a new file named `day1-hello.html`.
   - Type this minimal structure by hand (do NOT copy‑paste if possible):

     ```html
     <!DOCTYPE html>
     <html lang="en">
       <head>
         <meta charset="UTF-8">
         <title>Hello World</title>
       </head>
       <body>
         <h1>Hello, Web!</h1>
         <p>This is my first web page.</p>
       </body>
     </html>
     ```

4. **Open the file in your browser**
   - Option 1: Right‑click in VS Code → “Reveal in File Explorer/Finder” → double‑click `day1-hello.html`.
   - Option 2: Install the “Live Server” extension in VS Code and use it:
     - Right‑click in editor → “Open with Live Server”.
   - Observe the page in the browser.

### Practice Exercises
- Change the `<title>` text and confirm the browser tab title changes.
- Add another paragraph `<p>` with any text.
- Add your name in a `<h2>` heading.

### Reflection & Self‑Check
- In your own words (write this in a text file `notes-day1.md`):
  - What is a client? A server?
  - What is the difference between HTML and CSS?
- Question yourself: When I refresh the browser, what file is it actually reading? Where is that file stored?

### Optional Stretch
- Try creating a second HTML file `about-me.html` with:
  - A title and one paragraph about yourself.
  - Open it in the browser independently.

---

## Day 2 – HTML Document Structure & Text Content

### Goals
- Learn the basic structure of an HTML document.
- Use headings, paragraphs, and semantic sections.

### Concepts to Learn
- HTML document parts:
  - `<!DOCTYPE html>`, `<html>`, `<head>`, `<body>`.
- Text elements:
  - Headings: `<h1>` to `<h6>`.
  - Paragraphs: `<p>`.
  - Line breaks: `<br>` (use sparingly).
- Semantic structure:
  - `<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`.

### Step‑by‑Step Tasks

1. **Review and extend yesterday’s file**
   - Make a copy of `day1-hello.html` as `day2-structure.html`.
   - In `day2-structure.html`, replace the `<body>` with:

     ```html
     <body>
       <header>
         <h1>My First Structured Page</h1>
       </header>

       <main>
         <section>
           <h2>Introduction</h2>
           <p>Write a short introduction about yourself here.</p>
         </section>

         <section>
           <h2>Goals for Learning Web Development</h2>
           <p>Write 3–5 sentences about why you want to learn web development.</p>
         </section>
       </main>

       <footer>
         <p>© 2025 Your Name</p>
       </footer>
     </body>
     ```

   - Replace “Your Name” with your real name.

2. **Experiment with headings**
   - Under one of the sections, add headings from `<h3>` to `<h6>` with example text.
   - Open in browser and see how their sizes differ.

3. **Study: what makes HTML “semantic”?**
   - Think: Why use `<main>` instead of just a `<div>`?  
     Write your guess in `notes-day2.md`.

### Practice Exercises
- Add a new section:
  - Title: “Hobbies”.
  - Use a `<h2>` heading and one or two paragraphs describing your hobbies.
- Add another footer line (maybe an email or small motto).

### Reflection & Self‑Check
- Can you explain:
  - Why a page should have only one `<h1>` in most cases?
  - What `<header>` and `<footer>` generally represent?
- Re‑read your HTML and check:
  - Is the structure readable even without CSS?
  - Are headings used in a logical hierarchy?

### Optional Stretch
- Try rearranging your sections and see how easy it is to move whole sections thanks to semantic tags.
- Think: How would a screen reader (for visually impaired users) benefit from semantic HTML?

---

## Day 3 – Lists, Links, Images & Basic Forms

### Goals
- Use lists for structured content.
- Add internal and external links.
- Include images.
- Create a very simple HTML form.

### Concepts to Learn
- Lists:
  - Unordered lists: `<ul>` with `<li>`.
  - Ordered lists: `<ol>` with `<li>`.
- Links:
  - `<a href="...">`.
  - Absolute vs relative URLs.
- Images:
  - `<img src="..." alt="...">`.
  - Importance of `alt` text.
- Forms (very basic today):
  - `<form>`, `<input>`, `<label>`, `<button>`.

### Step‑by‑Step Tasks

1. **Create `day3-content.html`**
   - Start from the structure of `day2-structure.html` (copy & modify).
   - Add a new section to `<main>`:

     ```html
     <section>
       <h2>My Favorite Books</h2>
       <ul>
         <li>Book 1</li>
         <li>Book 2</li>
         <li>Book 3</li>
       </ul>
     </section>
     ```

   - Replace “Book 1/2/3” with real books, movies, or anything you like.

2. **Add an ordered list**
   - Add a section “Top 3 Learning Goals This Month” using an `<ol>` for the goals.

3. **Add links**
   - In a new section “Useful Links”:
     - Add a link to a website you like:
       ```html
       <a href="https://example.com" target="_blank">A Site I Like</a>
       ```
     - Add a link that goes back to the top of the page using an `id`:
       - Give your `<header>` an `id="top"`.
       - Add a link somewhere: `<a href="#top">Back to top</a>`.

4. **Add an image**
   - Find a small image file (e.g., `profile.jpg`) and save it in your project folder.
   - Add:

     ```html
     <section>
       <h2>About Me (With Image)</h2>
       <img src="profile.jpg" alt="A photo of me">
       <p>Write a short caption about the photo.</p>
     </section>
     ```

   - If you don’t have an image, at least write the `<img>` tag and imagine the file.

5. **Create a simple contact form**
   - At the bottom of `<main>`:

     ```html
     <section>
       <h2>Contact Me</h2>
       <form>
         <label for="name">Name:</label>
         <input id="name" type="text">

         <br><br>

         <label for="message">Message:</label>
         <input id="message" type="text">

         <br><br>

         <button type="submit">Send</button>
       </form>
     </section>
     ```

   - Load in browser and submit the form to see what happens (it will reload the page).

### Practice Exercises
- Add another field to the form, like “Email”.
- Add a second image (maybe a logo or icon) and experiment with changing `alt`.

### Reflection & Self‑Check
- In `notes-day3.md`, answer:
  - When should you use `ul` vs `ol`?
  - Why is `alt` text important for images?
  - What happens when you submit the form right now? Why?

### Optional Stretch
- Add a link in your footer that uses `mailto:youremail@example.com`.
- Make a mini “table of contents” at the top with links to each section using `href="#section-id"`.

---

## Day 4 – Intro to CSS: Selectors, Colors, Fonts

### Goals
- Connect a CSS file to your HTML.
- Style text, colors, and basic appearance.
- Understand CSS selectors and the idea of classes.

### Concepts to Learn
- Linking CSS:
  - `<link rel="stylesheet" href="styles.css">` in `<head>`.
- Selectors:
  - Element selectors: `p`, `h1`.
  - Class selectors: `.classname`.
  - ID selectors: `#idname`.
- Basic properties:
  - `color`, `background-color`, `font-size`, `font-family`, `text-align`.

### Step‑by‑Step Tasks

1. **Create a CSS file and connect it**
   - Copy `day3-content.html` to `day4-style.html`.
   - In the `<head>` of `day4-style.html`, add:

     ```html
     <link rel="stylesheet" href="day4-styles.css">
     ```

   - Create a new file `day4-styles.css` in the same folder.

2. **Add basic styles**
   - In `day4-styles.css`, add:

     ```css
     body {
       font-family: Arial, sans-serif;
       background-color: #f4f4f4;
       color: #333333;
     }

     h1, h2 {
       color: #2c3e50;
     }

     footer {
       text-align: center;
     }
     ```

   - Refresh `day4-style.html` in the browser and observe changes.

3. **Use classes**
   - In `day4-style.html`, pick one paragraph and add:
     ```html
     <p class="highlight">This is an important sentence.</p>
     ```
   - In `day4-styles.css`, add:
     ```css
     .highlight {
       background-color: #ffeaa7;
       font-weight: bold;
     }
     ```

4. **Use IDs**
   - Choose one section and add an `id`, e.g., `<section id="intro">`.
   - In CSS:
     ```css
     #intro {
       border-bottom: 2px solid #ccc;
       padding-bottom: 10px;
     }
     ```

### Practice Exercises
- Change the font for headings only (e.g., to `Georgia`).
- Center the `<h1>` text using `text-align: center;`.
- Make all links a specific color and remove the underline:

  ```css
  a {
    color: #2980b9;
    text-decoration: none;
  }
  ```

### Reflection & Self‑Check
- In `notes-day4.md`, write:
  - In your own words, what is a CSS selector?
  - Difference between a class and an ID (and when to use each).
- Open DevTools in your browser (right‑click → Inspect):
  - Click an element and see which CSS rules are applied.

### Optional Stretch
- Change the background color of the `body` when you hover over it (for fun).
- Experiment with a Google Font (read how to include one, then use it for headings).

---

## Day 5 – The Box Model: Margin, Padding, Borders & Basic Layout

### Goals
- Understand the CSS box model.
- Adjust spacing around and inside elements.
- Create a simple header–main–footer layout that doesn’t look cramped.

### Concepts to Learn
- Box model:
  - `content`, `padding`, `border`, `margin`.
- Common properties:
  - `margin`, `padding`, `border`, `width`, `height`.
- Display:
  - Block vs inline elements (intro level).

### Step‑by‑Step Tasks

1. **Create `day5-layout.html` and `day5-styles.css`**
   - Copy your `day4-style.html` and styles to new files for Day 5.
   - Keep the same sections; now focus on spacing.

2. **Experiment with box model**
   - In `day5-styles.css`, add:

     ```css
     header, main, footer {
       border: 1px solid #ddd;
       padding: 20px;
       margin: 10px;
       background-color: #ffffff;
     }
     ```

   - Refresh and observe the spacing and borders.

3. **Adjust spacing for readability**
   - Add some spacing between headings and paragraphs:

     ```css
     h1, h2 {
       margin-bottom: 10px;
     }

     p {
       line-height: 1.6;
       margin-bottom: 10px;
     }
     ```

4. **Understand block vs inline**
   - In HTML, wrap a few words in a paragraph with `<span class="keyword">...</span>`.
   - In CSS:

     ```css
     .keyword {
       background-color: #d1f7c4;
     }
     ```

   - Notice how inline elements (like `span`) don’t break the line, unlike block elements (`div`, `p`, `section`).

5. **Create a simple centered page layout**
   - In CSS:

     ```css
     body {
       font-family: Arial, sans-serif;
       background-color: #f4f4f4;
       color: #333333;
       margin: 0;
     }

     .container {
       max-width: 800px;
       margin: 0 auto;
       padding: 20px;
     }
     ```

   - In HTML, wrap your main content inside:

     ```html
     <body>
       <div class="container">
         <!-- header, main, footer go here -->
       </div>
     </body>
     ```

   - This centers the content and gives a nice page look.

### Practice Exercises
- Add `margin-top` to your `<main>` so it’s not stuck to the header.
- Give your footer less padding than header and main and see the visual difference.
- Experiment by changing `max-width` of `.container` to 600px and 1000px.

### Reflection & Self‑Check
- In `notes-day5.md`, draw (even roughly) the box model for one element, labeling content, padding, border, margin.
- Inspect a `<section>` in DevTools and look at the “Box Model” / “Layout” panel to see actual sizes.

### Optional Stretch
- Create a small “card” component:
  - A `div` with a border, padding, slightly different background, and some text.
  - Use `class="card"`; style it and reuse it for 2–3 cards.

---

## Day 6 – Building Your Personal Bio Page (Structure & Content)

### Goals
- Plan and structure a small “personal bio”/mini‑portfolio page.
- Use semantic HTML and everything from Days 1–5 without new concepts.

### Concepts to Reinforce
- Semantic structure (`header`, `nav`, `main`, `section`, `footer`).
- Headings hierarchy.
- Lists, links, images, simple form.
- Clean, readable HTML.

### Step‑by‑Step Tasks

1. **Plan your page (very important—no code yet for ~15–20 min)**
   - On paper or in a text file, outline:
     - Sections you want: e.g., “About Me”, “Skills”, “Hobbies”, “Contact”.
     - What content goes into each (bullets are fine).
   - Think: What should be inside `<header>`? What about `<footer>`?

2. **Create `day6-bio.html`**
   - Start from a blank file and write a full HTML page structure yourself (try not to copy older files, but you can look if stuck).
   - Include:
     - `<!DOCTYPE html>`, `<html>`, `<head>`, `<body>`.
     - A `<header>` with your name and possibly a simple `<nav>` (even plain text links).
     - A `<main>` with at least 3 sections.
     - A `<footer>`.

3. **Fill in real content**
   - “About Me” section:
     - A short paragraph describing who you are.
   - “Skills” section:
     - Use a list (`ul`) of things you want to learn or know a bit of (HTML, CSS, etc.).
   - “Hobbies” section:
     - A short description, maybe an image if you like.
   - “Contact” section:
     - A simple form (name, email, message) or at least your email in a link.

4. **Add meaningful IDs and classes**
   - Give each major section an `id` (e.g., `id="about"`, `id="skills"`).
   - Plan to style some things later with classes, e.g., `class="section-title"` for `<h2>` elements.

### Practice Exercises
- Add internal navigation:
  - In `<nav>`, add links like `<a href="#about">About</a>`.
- Add at least one external link (e.g., to your GitHub, or a site you like).

### Reflection & Self‑Check
- In `notes-day6.md`, answer:
  - Is your headings hierarchy logical? (Only one `<h1>`, then `<h2>` for major sections.)
  - Could someone understand your page’s structure by reading only the HTML (without CSS)?

### Optional Stretch
- Create an additional section “Timeline” using an ordered list for past/desired milestones (e.g., “Start learning HTML”, “Build first project”, etc.).
- Add a motto or short quote in `<blockquote>`.

---

## Day 7 – Styling Your Personal Bio Page (CSS + Basic Responsiveness)

### Goals
- Style your personal bio page to look clean and readable.
- Practice connecting HTML structure with CSS.
- Get a first taste of “responsive” by making sure it’s not broken on mobile.

### Concepts to Learn/Reinforce
- Reusing background, text, spacing styles.
- Class‑based styling.
- Basic responsiveness ideas (no complex layout yet):
  - `max-width`, fluid width, and the viewport meta tag.

### Step‑by‑Step Tasks

1. **Create a CSS file for your bio page**
   - In the same folder, create `day7-bio.css`.
   - In `day6-bio.html` (rename to `day7-bio.html` if you like), add in `<head>`:

     ```html
     <link rel="stylesheet" href="day7-bio.css">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     ```

   - The viewport meta tag helps with mobile scaling.

2. **Basic global styles**
   - In `day7-bio.css`, add:

     ```css
     * {
       box-sizing: border-box;
     }

     body {
       font-family: Arial, sans-serif;
       margin: 0;
       background-color: #f4f4f4;
       color: #333333;
     }

     .container {
       max-width: 900px;
       margin: 0 auto;
       padding: 20px;
       background-color: #ffffff;
     }
     ```

   - Wrap your page content in `<div class="container">` inside `<body>` if you haven’t already.

3. **Header & nav styling**
   - Assign classes/structure in HTML if needed (e.g., `<header class="site-header">`).
   - In CSS:

     ```css
     .site-header {
       padding: 20px 0;
       border-bottom: 1px solid #ddd;
       text-align: center;
     }

     nav a {
       margin: 0 10px;
       text-decoration: none;
       color: #2980b9;
     }

     nav a:hover {
       text-decoration: underline;
     }
     ```

4. **Section styling**
   - Give section titles a common class in HTML (`class="section-title"`).
   - In CSS:

     ```css
     .section-title {
       margin-top: 30px;
       margin-bottom: 10px;
       color: #2c3e50;
     }

     section {
       margin-bottom: 20px;
     }
     ```

5. **Footer styling**
   - Simple centered footer:

     ```css
     footer {
       text-align: center;
       padding: 10px;
       font-size: 0.9rem;
       color: #777;
       border-top: 1px solid #ddd;
       margin-top: 20px;
     }
     ```

6. **Check basic responsiveness**
   - Open your page in the browser.
   - Shrink the width of the browser window:
     - Does content stay readable?
     - Does any text overflow or become too small?
   - If something is too wide (e.g., an image), add:

     ```css
     img {
       max-width: 100%;
       height: auto;
     }
     ```

### Practice Exercises
- Choose a color palette (3–4 colors) and apply it consistently:
  - Background, headings, links, buttons (if any).
- Make sure text has enough contrast to be easily readable.

### Reflection & Self‑Check
- In `notes-day7.md`:
  - What parts of styling felt easy? What felt confusing?
  - Could you explain to someone else how to connect a CSS file to an HTML file?
- Open DevTools on your phone or use your browser’s device toolbar (toggle device mode) to see how your page looks on a small screen.

### Optional Stretch
- Add a simple “button‑like” style to one of your links:

  ```css
  .btn {
    display: inline-block;
    padding: 8px 16px;
    background-color: #2980b9;
    color: white;
    border-radius: 4px;
    text-decoration: none;
  }

  .btn:hover {
    background-color: #1f6391;
  }
  ```

  - Apply `class="btn"` to a link like “Contact Me”.

---

## How to Challenge Yourself This Week

- **Try before searching:**  
  When stuck, first guess or test an idea, then look up the answer.
- **Use DevTools daily:**  
  Inspect elements, see which CSS rules apply, and toggle them on/off.
- **Keep notes:**  
  Your `notes-dayX.md` files are the start of your personal “web dev notebook”. Write down both what you learned and what confused you.

If you want, next I can create a similarly structured Day 8–14 plan for Week 2 focusing on CSS layout and your first full portfolio draft.
