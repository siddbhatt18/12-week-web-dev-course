### Week 2 – Day‑by‑Day Plan (Days 1–7)

**Theme of Week 2:**  
Learn modern CSS layout (Flexbox) + basic responsive design and create your first version of a **personal portfolio website**, tracking it with **Git/GitHub**.

You can assume ~1.5–3 hours per day. If you have more time, spend it on the practice/optional stretches.

---

## Day 1 – Flexbox Basics + Git Initialization

### Goals
- Understand what Flexbox is and when to use it.
- Use Flexbox for simple horizontal and vertical alignment.
- Initialize a Git repository for your portfolio.

### Concepts to Learn
- Flexbox container and items:
  - `display: flex;`
  - Main axis vs cross axis.
- Core Flexbox properties:
  - `flex-direction`, `justify-content`, `align-items`.
- Git basics:
  - What a repository is.
  - `git init`, `git status`, `git add`, `git commit`.

### Step‑by‑Step Tasks

1. **Create a new project folder**
   - Make a folder: `portfolio-week2`.
   - Copy your best HTML/CSS from Week 1 (probably `day7-bio.html` and `day7-bio.css`) into this folder as:
     - `index.html`
     - `styles.css`
   - Open `portfolio-week2` in VS Code.

2. **Initialize Git**
   - Open a terminal in VS Code (View → Terminal).
   - Run:
     ```bash
     git init
     git status
     ```
   - You should see untracked files.

3. **Make your first commit**
   - Run:
     ```bash
     git add .
     git commit -m "Initial commit: base bio page"
     ```
   - Don’t worry if the message is simple. The key is: small, clear steps.

4. **Turn your header into a Flexbox layout**
   - In `index.html`, ensure your header looks something like:

     ```html
     <header class="site-header">
       <div class="logo">Your Name</div>
       <nav class="main-nav">
         <a href="#about">About</a>
         <a href="#projects">Projects</a>
         <a href="#contact">Contact</a>
       </nav>
     </header>
     ```

   - In `styles.css`, at the top of your header styles:

     ```css
     .site-header {
       display: flex;
       justify-content: space-between;
       align-items: center;
       padding: 20px 0;
       border-bottom: 1px solid #ddd;
     }

     .logo {
       font-weight: bold;
       font-size: 1.2rem;
     }

     .main-nav a {
       margin-left: 15px;
     }
     ```

   - Observe how the logo and nav now sit on one line and are spaced apart.

5. **Create a simple horizontal “skills bar” using Flexbox**
   - In your “Skills” section, structure it like:

     ```html
     <section id="skills">
       <h2 class="section-title">Skills</h2>
       <div class="skills-list">
         <span class="skill-pill">HTML</span>
         <span class="skill-pill">CSS</span>
         <span class="skill-pill">JavaScript</span>
       </div>
     </section>
     ```

   - In `styles.css`:

     ```css
     .skills-list {
       display: flex;
       gap: 10px; /* space between items */
       flex-wrap: wrap; /* allow wrapping on small screens */
     }

     .skill-pill {
       padding: 5px 10px;
       border-radius: 999px;
       background-color: #e0f1ff;
       font-size: 0.9rem;
     }
     ```

### Practice Exercises
- Change `justify-content` for `.skills-list` between:
  - `flex-start`, `center`, `space-between`, `space-around`.
- Add one more Flexbox row:
  - For example, a section of “Social links” laid out horizontally with icons or text links.

### Reflection & Self‑Check
- In `notes-week2-day1.md`, answer:
  - What changes when you switch `flex-direction` from `row` to `column`?
  - What problem does Flexbox solve that plain block elements + margins do not?

### Optional Stretch
- Try `align-items: center` vs `flex-start` vs `flex-end` in your header and observe how the nav links align vertically relative to the logo.

---

## Day 2 – Deeper Flexbox: Layouts & Cards

### Goals
- Understand how Flexbox distributes space using `flex-grow`, `flex-shrink`, and `flex-basis`.
- Create a responsive “cards” layout for your projects section.
- Make at least one meaningful Git commit.

### Concepts to Learn
- Advanced Flexbox properties:
  - `flex: 1;` shorthand (`flex-grow flex-shrink flex-basis`).
  - `flex-wrap`.
- Card/grid‑like layouts using Flexbox.

### Step‑by‑Step Tasks

1. **Add a “Projects” section with cards**
   - In `index.html`, create:

     ```html
     <section id="projects">
       <h2 class="section-title">Projects</h2>
       <div class="projects-grid">
         <article class="project-card">
           <h3>Project One</h3>
           <p>Short description of Project One.</p>
         </article>

         <article class="project-card">
           <h3>Project Two</h3>
           <p>Short description of Project Two.</p>
         </article>

         <article class="project-card">
           <h3>Project Three</h3>
           <p>Short description of Project Three.</p>
         </article>
       </div>
     </section>
     ```

2. **Style the cards with Flexbox**
   - In `styles.css`:

     ```css
     .projects-grid {
       display: flex;
       flex-wrap: wrap;
       gap: 20px;
       margin-top: 10px;
     }

     .project-card {
       flex: 1 1 250px; /* grow, shrink, base width */
       border: 1px solid #ddd;
       padding: 15px;
       border-radius: 6px;
       background-color: #fafafa;
     }
     ```

   - Resize the browser window and observe how cards wrap.

3. **Experiment with `flex` values**
   - Change `.project-card`:
     - Try `flex: 0 1 250px;` (no grow).
     - Try `flex: 2 1 250px;` (grow more).
   - Note how cards behave differently when the space is wide or narrow.

4. **Commit your progress**
   - In the terminal:
     ```bash
     git status
     git add .
     git commit -m "Add projects section with flexbox card layout"
     ```

### Practice Exercises
- Add a fourth project card; ensure it still looks okay on various widths.
- Add a subtle hover effect to `.project-card` (e.g., change background color or add shadow).

### Reflection & Self‑Check
- In `notes-week2-day2.md`, answer:
  - What does each part of `flex: 1 1 250px` represent?
  - When would you want items to wrap vs stay on one line?

### Optional Stretch
- Create a “two‑column” layout using Flexbox:
  - A left column for a profile picture and a right column for text in the About section:
    - `.about-layout { display: flex; gap: 20px; }`
    - Two child elements inside with different `flex` values.

---

## Day 3 – Responsive Design Basics & Media Queries

### Goals
- Understand the idea of mobile‑first vs desktop‑first design.
- Use relative units (`%`, `rem`) and media queries.
- Make your portfolio look good on both mobile and desktop.

### Concepts to Learn
- Responsive design basics:
  - Why fixed widths alone are problematic.
  - Mobile‑first thinking.
- Relative units:
  - `rem`, `%`, `vw`.
- Media queries:
  - `@media (max-width: 768px) { ... }`.

### Step‑by‑Step Tasks

1. **Review your current layout on different widths**
   - Shrink the browser to a narrow width.
   - Note:
     - Does the header wrap strangely?
     - Are texts too small or squished?
   - Write quick notes in `notes-week2-day3.md` about what looks wrong.

2. **Switch some sizes to relative units**
   - In `styles.css`, find any large font sizes or widths in `px` and experiment:
     - Font sizes: use `rem` (e.g., `font-size: 1rem`, `1.2rem`, `1.5rem`).
     - Layout width is already using `max-width: 900px` which is fine; keep that.

3. **Add a media query for small screens**
   - At the bottom of `styles.css`:

     ```css
     @media (max-width: 600px) {
       .site-header {
         flex-direction: column;
         align-items: flex-start;
       }

       .main-nav a {
         display: inline-block;
         margin: 5px 10px 0 0;
       }

       .projects-grid {
         flex-direction: column;
       }
     }
     ```

   - Test on narrow width (~375–500px) to see the stacked layout.

4. **Adjust spacing for mobile**
   - Inside the same media query, maybe reduce paddings/margins:

     ```css
     @media (max-width: 600px) {
       .container {
         padding: 10px;
       }

       .section-title {
         margin-top: 20px;
       }
     }
     ```

### Practice Exercises
- Try a second breakpoint, e.g., `@media (max-width: 900px)` to slightly adjust layout for tablets.
- Adjust image styles so they never overflow the container:
  ```css
  img {
    max-width: 100%;
    height: auto;
    display: block;
  }
  ```

### Reflection & Self‑Check
- In `notes-week2-day3.md`, answer:
  - In your own words, what does “mobile‑first” mean?
  - Why might `max-width` be better than `width` in many layouts?

### Optional Stretch
- Use your browser’s Device Toolbar (responsive mode) and test your page as if it were on different phones (e.g., iPhone SE, Pixel).  
  Write down which sizes break your layout and fix at least one issue using a media query.

---

## Day 4 – Planning & Structuring the Full Portfolio

### Goals
- Plan the final structure of your portfolio for this week.
- Make your HTML more complete and intentional (not just placeholders).
- Add or refine sections: About, Projects, Skills, Contact.

### Concepts to Reinforce
- Semantic HTML with clear sections.
- Logical navigation.
- Writing meaningful content (even if short).

### Step‑by‑Step Tasks

1. **Plan your portfolio (no code for 20–30 minutes)**
   - Outline your sections and content in a text file or on paper:
     - About (who you are, your goals).
     - Skills (technologies, soft skills).
     - Projects (at least 3 project slots, even if some are “future projects”).
     - Contact (email, links, maybe a simple form).
   - For each section:
     - What is the main message?
     - What should a visitor learn/feel?

2. **Refine HTML structure**
   - In `index.html`, ensure:
     - Only one `<h1>` for site title/name.
     - Each section has:
       - A unique `id` (for nav).
       - A clear `<h2>` heading.
   - Flesh out content:
     - Replace placeholder “Project One” text with at least a 1–2 sentence description.
     - In About, clearly state:
       - What you’re learning now.
       - What kind of projects you want to build.

3. **Add external links**
   - In Contact section, add:
     - A mailto link: `<a href="mailto:your@email.com">Email me</a>`.
     - If you have GitHub/LinkedIn, add those links.
   - Style them lightly (reuse nav/link styles).

4. **Commit your structural changes**
   - In the terminal:

     ```bash
     git status
     git add index.html
     git commit -m "Refine portfolio structure and content"
     ```

### Practice Exercises
- Add a small “Intro/Hero” section near the top:
  - Brief tagline like “Aspiring Full Stack Web Developer”.
  - One button (styled link) like “View my projects”.
- Ensure all internal nav links (`href="#section-id"`) work correctly.

### Reflection & Self‑Check
- In `notes-week2-day4.md`, answer:
  - Does the structure of your page make sense if someone scrolls from top to bottom?
  - If a recruiter had only 30 seconds, what would they notice first?

### Optional Stretch
- Add a simple HTML “timeline” (like in Week 1 optional):
  - Use an ordered list under a “Learning Journey” section.

---

## Day 5 – Visual Design & Layout Polish

### Goals
- Improve the visual design: consistent spacing, typography, and colors.
- Make the hero/About sections feel more “designed”, not just plain text.
- Practice thinking critically about your layout instead of randomly changing styles.

### Concepts to Reinforce
- Consistent spacing scale (e.g., 4/8/12/16/24px).
- Typography hierarchy:
  - Size, weight, and color to show importance.
- Visual grouping using spacing and borders/backgrounds.

### Step‑by‑Step Tasks

1. **Define a simple design system in CSS**
   - Near the top of `styles.css`, add comments and base styles:

     ```css
     /* Color palette
        Primary: #2980b9
        Accent: #e67e22
        Light background: #f4f4f4
        Card background: #ffffff
     */

     body {
       font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
       background-color: #f4f4f4;
       color: #333;
       margin: 0;
     }

     h1 {
       font-size: 2rem;
     }

     h2 {
       font-size: 1.5rem;
       margin-top: 2rem;
       margin-bottom: 0.5rem;
     }

     p {
       line-height: 1.6;
       margin-bottom: 0.75rem;
     }
     ```

2. **Improve the hero/About section layout**
   - Wrap your hero/About content:

     ```html
     <section id="about" class="hero">
       <div class="hero-text">
         <h1>Your Name</h1>
         <p>Short clear tagline about you.</p>
         <a href="#projects" class="btn">View My Projects</a>
       </div>
       <div class="hero-image">
         <!-- optional image or placeholder -->
       </div>
     </section>
     ```

   - In CSS:

     ```css
     .hero {
       display: flex;
       align-items: center;
       justify-content: space-between;
       gap: 20px;
       margin-top: 20px;
     }

     .hero-text {
       flex: 1;
     }

     .hero-image {
       flex: 1;
       min-height: 150px;
       background-color: #ddeeff; /* placeholder */
       border-radius: 8px;
     }

     .btn {
       display: inline-block;
       padding: 8px 16px;
       background-color: #2980b9;
       color: #fff;
       border-radius: 4px;
       text-decoration: none;
       margin-top: 10px;
     }

     .btn:hover {
       background-color: #1f6391;
     }
     ```

3. **Polish the Projects section visually**
   - Add a subtle shadow and hover effect to `.project-card`:

     ```css
     .project-card {
       flex: 1 1 250px;
       border-radius: 6px;
       background-color: #ffffff;
       padding: 15px;
       box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
       transition: transform 0.1s ease, box-shadow 0.1s ease;
     }

     .project-card:hover {
       transform: translateY(-2px);
       box-shadow: 0 4px 8px rgba(0, 0, 0, 0.12);
     }
     ```

4. **Commit your visual polish**
   - In terminal:

     ```bash
     git add styles.css index.html
     git commit -m "Improve hero and projects section design"
     ```

### Practice Exercises
- Standardize margins between sections (e.g., always `margin-bottom: 32px`).
- Make all headings use the same color (primary color).

### Reflection & Self‑Check
- In `notes-week2-day5.md`, answer:
  - Which part of your page looks the most polished? Why?
  - Which part looks least polished? What would you change?

### Optional Stretch
- Look at 2–3 simple portfolio sites online.  
  Identify one design idea from each and try to apply a small version of it to your page (e.g., a colored strip behind the header, a subtle border line, different font choice).

---

## Day 6 – Accessibility, Details & More Git/GitHub

### Goals
- Improve accessibility basics (alt text, heading structure, color contrast).
- Create a GitHub repository and push your portfolio.
- Fix small layout issues you notice.

### Concepts to Learn
- Basic accessibility:
  - `alt` text, focus styles on links/buttons.
  - Sufficient color contrast.
- GitHub:
  - Remote repositories.
  - `git remote add`, `git push`.

### Step‑by‑Step Tasks

1. **Accessibility pass**
   - Check all `<img>` tags:
     - Do they have meaningful `alt` attributes?
   - Check headings:
     - Only one `<h1>`.
     - Logical order for others.
   - Check links and buttons:
     - Are they recognizable as interactive? (color, underline, hover).
   - If you made your links look like plain text, ensure they at least change color/underline on hover and keyboard focus.

   - In CSS, ensure focus is visible:

     ```css
     a:focus,
     button:focus {
       outline: 2px solid #e67e22;
       outline-offset: 2px;
     }
     ```

2. **Create a GitHub repo**
   - Go to GitHub → “New repository”.
   - Name it `portfolio` (or similar).
   - Do NOT add a README or .gitignore from GitHub (you already have a local repo).
   - Copy the “remote URL” GitHub shows (e.g., `https://github.com/yourname/portfolio.git`).

3. **Connect local repo to GitHub and push**
   - In terminal (inside `portfolio-week2`):

     ```bash
     git remote add origin YOUR_REMOTE_URL_HERE
     git branch -M main
     git push -u origin main
     ```

   - Reload GitHub page and confirm your files are visible.

4. **Fix at least one small layout issue**
   - Use DevTools to inspect something that looks “off” (e.g., a section that’s too tight).
   - Adjust margins/padding or font size until it feels better.

### Practice Exercises
- Test keyboard navigation:
  - Use Tab to move through links/buttons.
  - See where focus goes; ensure it makes sense.
- If any text is light gray on white, consider darkening it for contrast.

### Reflection & Self‑Check
- In `notes-week2-day6.md`, answer:
  - Why is keyboard navigation important?
  - What did you need to change to make your site more accessible?

### Optional Stretch
- Explore GitHub Pages documentation (don’t deploy yet unless you want to; we’ll touch on deployment again in later weeks).

---

## Day 7 – Final Portfolio Review & Mini Deployment (Optional)

### Goals
- Review and refine your portfolio as a “Week 2 milestone”.
- Fix remaining small issues in layout/responsiveness.
- Optionally, deploy via GitHub Pages so you can share a live link.

### Concepts to Reinforce
- Iterative improvement: small changes, test, repeat.
- Testing across devices/screen sizes.
- (Optional) Static site hosting with GitHub Pages.

### Step‑by‑Step Tasks

1. **Full walkthrough of your site**
   - Scroll top to bottom and use this checklist:
     - Header/nav clear and not cramped at any width.
     - Hero/About conveys who you are and what you’re doing.
     - Skills show at least a few relevant items.
     - Projects have clear titles and descriptions.
     - Contact section has at least email and/or external links.
     - Page looks OK on narrow and wide screens.

2. **Responsive test again**
   - Use DevTools device mode to test a few breakpoints:
     - ~375px wide (small phone).
     - ~768px (tablet).
     - ~1024px and up (laptop/desktop).
   - For any layout issue you see:
     - Write it down.
     - Decide: fix with Flexbox settings, margins, or a media query.
     - Implement and retest.

3. **Code cleanup**
   - Remove any unused CSS rules (e.g., random test selectors).
   - Check that classes/IDs are named reasonably (e.g., `hero-text` vs `stuff1`).
   - Ensure indentation in HTML and CSS is consistent.

4. **Final commit for Week 2**
   - In terminal:

     ```bash
     git status
     git add .
     git commit -m "Polish layout and responsiveness for week 2 milestone"
     git push
     ```

5. **(Optional) Deploy with GitHub Pages**
   - In GitHub repo:
     - Settings → Pages.
     - Source: select `main` branch, root folder.
   - Save and wait a minute; GitHub will give you a public URL.
   - Test your site via that URL.

### Practice Exercises
- Ask yourself:
  - If you had to remove one section, which would it be, and why?
  - If you had to improve one section next week, what would it be?

### Reflection & Self‑Check
- In `notes-week2-day7.md`, write:
  - 3 things you’re proud of about this portfolio.
  - 3 things you want to improve in the coming weeks (e.g., better images, more projects, smoother nav).
  - One Flexbox concept you still find confusing.

### Optional Stretch
- If GitHub Pages is working, share your link with a friend and ask:
  - “Is anything hard to read?”
  - “If you saw this and didn’t know me, what would you think I do?”

---

### How This Week Sets You Up for Next Weeks

By the end of Week 2 you should:
- Be comfortable using Flexbox for basic page layouts.
- Have a **live or at least repo‑ready portfolio** you’ll keep improving.
- Have a basic habit of:
  - Planning before coding.
  - Using DevTools to debug layout.
  - Committing and pushing to Git regularly.

If you’d like, next I can break down Week 3 (JavaScript fundamentals) into a similar Day‑by‑Day plan.
