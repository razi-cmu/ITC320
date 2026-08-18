# Week 7 — Deployment to a Server

## Why Deployment Matters

Every project built so far in this course has lived on one computer, opened directly from a local file or run through a local development setup. Deployment is the process of moving a finished project onto a server so that anyone with an internet connection and the right URL can visit it, not just the person who built it.

This matters for two reasons. First, it is the only way anyone outside this classroom will ever see the applications built here. Second, deployment exposes problems that never show up during local development. A page that works perfectly on a local machine can break once it is served from a different location, on a different operating system, with a different set of rules about file paths. Today's class is about understanding those rules before they cause confusion later.

## Static vs Dynamic Hosting, Revisited

Week 1 introduced the difference between static pages, which are the same for every visitor, and dynamic pages, which are generated or changed by a server before being sent to the browser.

That distinction matters here because it determines what kind of hosting a project needs:

| Hosting Type | What It Serves | Requires a Backend? |
| --- | --- | --- |
| Static hosting | HTML, CSS, JavaScript, images, and other files, exactly as written | No |
| Dynamic hosting | Pages generated or modified by server-side code (databases, user accounts, server logic) | Yes |

Everything built in this course so far is HTML, CSS, and client-side JavaScript. That means every project so far qualifies as a static site, and static hosting is all that is needed to publish it. 

## Comparing Hosting Options

There is no single correct way to host a static site. A few common options:

| Option | How It Works | Good Fit For |
| --- | --- | --- |
| Traditional web hosting (FTP/SFTP) | Files are uploaded directly to a hosting provider's server | Projects needing a custom domain, existing institutional hosting |
| Cloud static hosts (Netlify, Vercel, and similar) | Files are connected to an account and automatically published, often from a repository | Larger projects, teams wanting automatic redeploys |
| GitHub Pages | Files are stored in a GitHub repository and published directly from it, for free | Learning projects, portfolios, anything already tracked in Git |

This course uses GitHub Pages. It is free, it does not require setting up a hosting account separate from GitHub, and it introduces the basics of version control along the way, which is a useful skill on its own.

## Version Control Basics: What a Repository Is

GitHub Pages publishes files out of a GitHub repository, so a short introduction to what that means is worth covering before deploying anything.

A repository (often shortened to "repo") is a project folder that Git tracks the history of. Every time changes are saved to a repository, that snapshot is called a commit. Uploading a commit to GitHub is called a push.

For this class, the fastest path to a working repository does not require the command line. GitHub's website allows a repository to be created and files to be uploaded directly through the browser:

1. Log in to GitHub and select New repository.
2. Give the repository a name, for example `week7-deploy-practice`.
3. Leave the repository set to Public. GitHub Pages requires a public repository on a free account.
4. Select Create repository.
5. On the new repository's page, select Add file, then Upload files, and drag in the project's files and folders.
6. Scroll down and select Commit changes.

Students who already know Git and prefer the command line (`git init`, `git add`, `git commit`, `git push`) are welcome to use it instead. Both paths end with the same result: the project's files living in a GitHub repository.

## The GitHub Pages Deployment Workflow

Once a repository holds the project's files, publishing it is a matter of turning on GitHub Pages:

1. Open the repository on GitHub and select Settings.
2. In the left sidebar, select Pages.
3. Under Build and deployment, set the Source to Deploy from a branch.
4. Set the branch to `main` and the folder to `/ (root)`, then select Save.
5. Wait a minute or two. GitHub Pages needs a short amount of time to build and publish the site.
6. Refresh the Pages settings screen. A green banner will show the live URL, typically in the form:

```
https://username.github.io/repository-name/
```

Visiting that URL loads the deployed project. Note the trailing folder in the URL: unlike a project running locally, a GitHub Pages project site is not hosted at the root of a domain, it is hosted inside a folder named after the repository. 

## File Paths and Case Sensitivity After Deployment

This is the section most likely to explain a "why isn't this working" moment during the exercise, so it deserves close attention.

**Local file paths are usually forgiving. Deployed file paths are not.**

Two habits that work fine on a local machine but can break once a project is deployed:

**Case sensitivity.** Windows and macOS file systems generally do not care whether a file is named `Photo.jpg` or `photo.jpg`, either one will match a reference to the other. GitHub Pages runs on a case-sensitive file system. If an HTML file references `images/Photo.jpg` but the actual file is saved as `images/photo.jpg`, the image loads locally but returns a 404 Not Found once deployed. The fix is to make sure the filename in the code matches the filename on disk exactly, capital letters included.

**Absolute vs relative paths.** A path like `/images/photo.jpg`, starting with a forward slash, is an absolute path. It tells the browser to look for `images/photo.jpg` starting from the root of the domain. On a local project, or on a site hosted at the root of a domain, that works fine. On a GitHub Pages project site, the project does not live at the root, it lives inside `/repository-name/`, so an absolute path like `/images/photo.jpg` actually points to `https://username.github.io/images/photo.jpg`, which does not exist. A relative path like `images/photo.jpg`, with no leading slash, tells the browser to look relative to the current page instead, and continues to work correctly no matter what folder the project is deployed into.

## Exercise: Build and Deploy a Portfolio Site

This exercise has two parts. Part 1 builds a small, working portfolio site, using image handling and events from Weeks 4 and 6. Part 2 deploys that finished site to GitHub Pages using the workflow covered earlier in this class.

### Part 1: Building the Site

`index.html` and `styles.css` are provided in full and already produce a finished, styled layout. Nothing needs to change in either file to make the page look complete. The JavaScript file, `script.js`, is written from scratch, in the steps below, to bring the page to life.

**index.html**

```html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Steve Jobs - Front-end Developer Portfolio</title>
  <meta
    name="description"
    content="Steve jobs is a front-end developer who builds clean, accessible, working interfaces."
  >

  <link rel="stylesheet" href="styles.css">
</head>

<body>
  <header class="site-header">
    <div class="header-inner">
      <a class="logo" href="#hero" aria-label="Steve Jobs home">
        Steve Jobs
      </a>

      <nav aria-label="Main navigation">
        <a href="#about">About</a>
        <a href="#projects">Projects</a>
      </nav>
    </div>
  </header>

  <main>
    <section class="hero" id="hero" aria-labelledby="hero-heading">
      <div
        class="slideshow"
        id="slideshow"
        aria-label="Featured work slideshow"
      >
        <img
          src="images/slide1.jpg"
          alt="Featured front-end development work"
          id="slide-image"
          class="slide-image"
          width="800"
          height="320"
        >

        <div class="slideshow-controls" aria-label="Slideshow controls">
          <button
            id="prev-btn"
            class="slide-btn"
            type="button"
            aria-label="Show previous slide"
          >
            Prev
          </button>

          <button
            id="play-pause-btn"
            class="slide-btn"
            type="button"
            aria-label="Pause slideshow"
            aria-pressed="false"
          >
            Pause
          </button>

          <button
            id="next-btn"
            class="slide-btn"
            type="button"
            aria-label="Show next slide"
          >
            Next
          </button>
        </div>
      </div>

      <div class="hero-text">
        <p class="eyebrow">Front-end development</p>
        <h1 id="hero-heading">I build clean, working interfaces.</h1>
        <p>
          I turn thoughtful designs into accessible, responsive web experiences
          from the ground up.
        </p>
      </div>
    </section>

    <section class="about" id="about" aria-labelledby="about-heading">
      <h2 id="about-heading">About</h2>

      <p class="bio-text">
        I'm a front-end developer who enjoys turning clean designs into
        working pages.
        <span id="bio-more" class="bio-more">
          I've spent the last few years focused on JavaScript, accessibility,
          and building small tools that make everyday tasks easier for the
          people using them.
        </span>
      </p>

    </section>

    <section class="projects" id="projects" aria-labelledby="projects-heading">
      <h2 id="projects-heading">Projects</h2>

      <div class="project-grid">
        <article class="project-card">
          <img
            src="images/project1.jpg"
            data-hover="images/project1-hover.jpg"
            alt="Landing Page Redesign project preview"
            class="project-image"
            width="600"
            height="400"
          >

          <div class="project-content">
            <h3>Landing Page Redesign</h3>
            <p>
              A rebuilt marketing page with a cleaner layout and improved
              visual hierarchy.
            </p>
          </div>
        </article>

        <article class="project-card">
          <img
            src="images/project2.jpg"
            data-hover="images/project2-hover.jpg"
            alt="Task Tracker project preview"
            class="project-image"
            width="600"
            height="400"
          >

          <div class="project-content">
            <h3>Task Tracker</h3>
            <p>
              A to-do list with filtering, simple styling, and an easy-to-use
              interface.
            </p>
          </div>
        </article>

        <article class="project-card">
          <img
            src="images/project3.jpg"
            data-hover="images/project3-hover.jpg"
            alt="Recipe Finder project preview"
            class="project-image"
            width="600"
            height="400"
          >

          <div class="project-content">
            <h3>Recipe Finder</h3>
            <p>
              A searchable collection of recipe cards designed for quick and
              simple browsing.
            </p>
          </div>
        </article>
      </div>
    </section>
  </main>

  <footer class="site-footer">
    <p>&copy; 2026 Steve Jobs. All rights reserved.</p>
  </footer>

  <script src="script.js"></script>
</body>
</html>
```

**styles.css**

```css

:root {
  --bg: #faf9f7;
  --surface: #ffffff;
  --text: #1f2430;
  --text-muted: #5b6472;
  --accent: #7c5cff;
  --accent-dark: #6548e8;
  --border: #e6e3de;
  --shadow: 0 10px 30px rgba(31, 36, 48, 0.08);
  --radius: 14px;
  --max-width: 1000px;
}


/* --------------------------------
   Reset / base
-------------------------------- */

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
  scroll-padding-top: 80px;
}

body {
  font-family:
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    Roboto,
    Helvetica,
    Arial,
    sans-serif;
  background-color: var(--bg);
  color: var(--text);
  line-height: 1.6;
}

img {
  max-width: 100%;
}

button,
a {
  font: inherit;
}


/* --------------------------------
   Header
-------------------------------- */

.site-header {
  position: sticky;
  top: 0;
  z-index: 10;
  background-color: rgba(255, 255, 255, 0.96);
  border-bottom: 1px solid var(--border);
  backdrop-filter: blur(8px);
}

.header-inner {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 16px 24px;

  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
}

.logo {
  color: var(--text);
  font-weight: 700;
  font-size: 1.2rem;
  text-decoration: none;
}

.logo:hover {
  color: var(--accent);
}

nav {
  display: flex;
  align-items: center;
  gap: 20px;
}

nav a {
  color: var(--text-muted);
  text-decoration: none;
  font-size: 0.95rem;
  transition: color 0.2s ease;
}

nav a:hover {
  color: var(--accent);
}


/* --------------------------------
   Hero
-------------------------------- */

.hero {
  max-width: var(--max-width);
  margin: 40px auto;
  padding: 0 24px;

  display: grid;
  grid-template-columns: 1.2fr 1fr;
  gap: 32px;
  align-items: center;
}

.slideshow {
  position: relative;
  overflow: hidden;
  border-radius: var(--radius);
  background-color: var(--surface);
  box-shadow: var(--shadow);
}

.slide-image {
  width: 100%;
  height: 320px;
  object-fit: cover;
  display: block;
}

.slideshow-controls {
  position: absolute;
  bottom: 12px;
  left: 50%;

  transform: translateX(-50%);

  display: flex;
  gap: 8px;

  width: max-content;
}

.slide-btn {
  background-color: rgba(0, 0, 0, 0.55);
  color: #fff;

  border: 1px solid rgba(255, 255, 255, 0.15);
  padding: 7px 14px;

  border-radius: 999px;
  font-size: 0.8rem;
  font-weight: 600;

  cursor: pointer;

  transition:
    background-color 0.2s ease,
    transform 0.2s ease;
}

.slide-btn:hover {
  background-color: rgba(0, 0, 0, 0.75);
}

.slide-btn:active {
  transform: scale(0.96);
}

.hero-text {
  max-width: 460px;
}

.eyebrow {
  margin-bottom: 8px;
  color: var(--accent);
  font-size: 0.85rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.hero-text h1 {
  margin-bottom: 12px;
  font-size: clamp(2rem, 4vw, 2.8rem);
  line-height: 1.15;
  letter-spacing: -0.03em;
}

.hero-text p:last-child {
  color: var(--text-muted);
}


/* --------------------------------
   About / Projects sections
-------------------------------- */

.about,
.projects {
  max-width: var(--max-width);
  margin: 60px auto;
  padding: 0 24px;
}

.about h2,
.projects h2 {
  margin-bottom: 16px;
  font-size: 1.5rem;
  line-height: 1.3;
}

.bio-text {
  max-width: 760px;
  color: var(--text-muted);
}


/* --------------------------------
   Project grid
-------------------------------- */

.project-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 24px;
}

.project-card {
  overflow: hidden;

  background-color: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius);

  box-shadow: var(--shadow);

  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
}

.project-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 14px 36px rgba(31, 36, 48, 0.11);
}

.project-image {
  width: 100%;
  height: 190px;

  display: block;

  object-fit: cover;

  border-bottom: 1px solid var(--border);

  cursor: default;
}

.project-content {
  padding: 16px;
}

.project-card h3 {
  margin-bottom: 6px;
  font-size: 1.1rem;
  line-height: 1.3;
}

.project-card p {
  color: var(--text-muted);
  font-size: 0.9rem;
}


/* --------------------------------
   Footer
-------------------------------- */

.site-footer {
  margin-top: 60px;
  padding: 32px 24px;

  text-align: center;

  color: var(--text-muted);
  border-top: 1px solid var(--border);

  font-size: 0.85rem;
}


/* --------------------------------
   Keyboard focus
-------------------------------- */

a:focus-visible,
button:focus-visible {
  outline: 3px solid rgba(124, 92, 255, 0.35);
  outline-offset: 3px;
}


```

Assets needed in an `images` folder alongside `index.html`: `slide1.jpg`, `slide2.jpg`, `slide3.jpg` for the hero slideshow, `project1.jpg` / `project1-hover.jpg`, `project2.jpg` / `project2-hover.jpg`, and `project3.jpg` / `project3-hover.jpg` for the project cards.

Build `script.js`, to incorporate all the features based on the HTML.


### Solution

```javascript
const slideImage = document.getElementById("slide-image");
const prevBtn = document.getElementById("prev-btn");
const nextBtn = document.getElementById("next-btn");
const playPauseBtn = document.getElementById("play-pause-btn");
const slideshow = document.getElementById("slideshow");

const slidePaths = [
  "images/slide1.jpg",
  "images/slide2.jpg",
  "images/slide3.jpg",
];

const slideAlts = [
  "Featured front-end development work",
  "Responsive web development project displayed on a laptop",
  "Modern front-end development workspace",
];

let currentSlide = 0;
let isPlaying = true;
let slideTimer = null;

/* --------------------------------
   Image preloading
-------------------------------- */

const preloadedImages = [];

slidePaths.forEach(function (path) {
  const img = new Image();
  img.src = path;
  preloadedImages.push(img);
});

/* --------------------------------
   Slideshow
-------------------------------- */

function showSlide(index) {
  if (!slideImage) {
    return;
  }

  currentSlide = index;
  slideImage.src = slidePaths[currentSlide];
  slideImage.alt = slideAlts[currentSlide];
}

function nextSlide() {
  showSlide((currentSlide + 1) % slidePaths.length);
}

function prevSlide() {
  showSlide((currentSlide - 1 + slidePaths.length) % slidePaths.length);
}

function startSlideshow() {
  stopSlideshow();

  if (!isPlaying || slidePaths.length <= 1) {
    return;
  }

  slideTimer = setInterval(nextSlide, 4000);
}

function stopSlideshow() {
  if (slideTimer !== null) {
    clearInterval(slideTimer);
    slideTimer = null;
  }
}

function updatePlayPauseButton() {
  if (!playPauseBtn) {
    return;
  }

  playPauseBtn.textContent = isPlaying ? "Pause" : "Play";
  playPauseBtn.setAttribute(
    "aria-label",
    isPlaying ? "Pause slideshow" : "Play slideshow",
  );
  playPauseBtn.setAttribute("aria-pressed", String(!isPlaying));
}

if (slideImage && prevBtn && nextBtn && playPauseBtn && slideshow) {
  nextBtn.addEventListener("click", function () {
    nextSlide();

    if (isPlaying) {
      startSlideshow();
    }
  });

  prevBtn.addEventListener("click", function () {
    prevSlide();

    if (isPlaying) {
      startSlideshow();
    }
  });

  playPauseBtn.addEventListener("click", function () {
    isPlaying = !isPlaying;

    updatePlayPauseButton();

    if (isPlaying) {
      startSlideshow();
    } else {
      stopSlideshow();
    }
  });

  /* --------------------------------
   Project image hover previews
-------------------------------- */

  const projectImages = document.querySelectorAll(".project-image");

  projectImages.forEach(function (img) {
    const originalSrc = img.src;
    const hoverSrc = img.dataset.hover;

    /*
    Preload the hover image so switching doesn't cause
    a visible loading delay.
  */
    if (hoverSrc) {
      const hoverImage = new Image();
      hoverImage.src = hoverSrc;
    }

    img.addEventListener("mouseenter", function () {
      if (hoverSrc) {
        img.src = hoverSrc;
      }
    });

    img.addEventListener("mouseleave", function () {
      img.src = originalSrc;
    });

    /*
    If an image fails to load, return to the original image.
  */
    img.addEventListener("error", function () {
      if (img.src !== originalSrc) {
        img.src = originalSrc;
      }
    });
  });
}
```

### Part 2: Deploying the Site

With the site fully working locally, the next step is publishing it, using the GitHub Pages workflow covered earlier in this class:

1. Create a new public GitHub repository and upload `index.html`, `styles.css`, `script.js`, and the `images` folder, keeping the folder structure intact.
2. Turn on GitHub Pages for the repository and open the live URL.


## References

- GitHub Docs, "Configuring a publishing source for your GitHub Pages site," https://docs.github.com/en/pages

