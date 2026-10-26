# Week 9: Browser Objects

Since Week 4, almost all of our JavaScript has talked to the page through the DOM: selecting elements, changing their text, and responding to clicks. But a web page lives inside a browser. The browser also gives JavaScript objects for other things:

- the browser window
- the address bar
- the tab's history
- the browser itself
- the device's screen
- a small amount of storage that survives a page reload

This week we look at those objects.

## The Browser Object Model

The objects the browser provides beyond the page itself are often called the Browser Object Model (BOM). The DOM is one part of it. At the top of everything sits the `window` object:

```
window
├── document         (the DOM, Weeks 4 to 6)
├── location         (the current URL)
├── history          (the pages visited in this tab)
├── navigator        (the browser and its environment)
├── screen           (the user's display)
├── localStorage     (data that survives closing the browser)
└── sessionStorage   (data that survives a reload, until the tab closes)
```

| | DOM | BOM |
| --- | --- | --- |
| Describes | The content of the page | The browser around the page |
| Entry point | `document` | `window` |
| Examples | `document.querySelector()`, `textContent`, `classList` | `location.href`, `history.back()`, `navigator.language` |

## The window Object

### window as the Global Object

Every global variable, function, and object in the browser belongs to `window`. That is why we have been able to write `alert()` and `document` all along without typing `window.` in front of them.

```js
console.log(window.document === document); // true
window.alert("Hello from window!");         // same as alert("Hello from window!")
```

This also explains one more difference between `var` and `let`/`const` from Week 2. A global variable declared with `var` becomes a property of `window`, while `let` and `const` do not:

```js
var siteName = "Trailhead";
let tagline = "Get outside";

console.log(window.siteName); // "Trailhead"
console.log(window.tagline);  // undefined
```

A `var` variable can accidentally overwrite a built-in `window` property with the same name. This is one more reason we use `let` and `const`.

### Dialog Methods: alert(), confirm(), and prompt()

You already know `alert()` and `prompt()` from Weeks 2 and 3. Both are methods of `window`. The third dialog method, `confirm()`, shows a message with OK and Cancel buttons and returns a boolean:

```js
const leave = confirm("Are you sure you want to leave this page?");

if (leave) {
  console.log("The user clicked OK.");
} else {
  console.log("The user clicked Cancel.");
}
```

Remember that `prompt()` returns a string, or `null` if the user clicks Cancel.

All three dialogs freeze the page until the user responds, and they cannot be styled. Use them sparingly. For most feedback, an on-page message (as we have done since Week 4) is the better choice. `confirm()` is still handy before an action that is hard to undo, such as leaving a page.

### Window Size

The area of the browser where the page is drawn is called the viewport. Its size is available through `window.innerWidth` and `window.innerHeight`, in pixels. `window.outerWidth` and `window.outerHeight` include the browser's toolbars and borders.

```js
console.log(`Viewport: ${window.innerWidth} x ${window.innerHeight}`);
console.log(`Whole browser window: ${window.outerWidth} x ${window.outerHeight}`);
```

When the user resizes the browser, `window` fires a `resize` event:

```html
<p id="size-info"></p>
```

```js
const sizeInfo = document.querySelector("#size-info");

function showSize() {
  sizeInfo.textContent = `Viewport: ${window.innerWidth} x ${window.innerHeight} pixels`;
}

window.addEventListener("resize", showSize);
showSize(); // show the size once when the page first loads
```

Drag the edge of your browser window and watch the numbers update. The `resize` event fires many times per second while dragging, so keep its handler short.

### Scrolling

`window.scrollY` tells you how far the page has scrolled down, in pixels. `window.scrollTo()` scrolls to a position. A common use is a "Back to top" button:

```js
const topBtn = document.querySelector("#top-btn");

topBtn.addEventListener("click", () => {
  window.scrollTo({ top: 0, behavior: "smooth" });
});
```

### Opening New Windows

`window.open()` opens a URL in a new tab or window:

```js
window.open("https://www.example.com", "_blank");
```

Browsers block pop-ups that open without user action, so `window.open()` only works reliably inside a click handler. In most cases a plain link with `target="_blank"` is simpler and more accessible, so reach for `window.open()` only when you need JavaScript to decide what to open.

## The location Object

### Reading URL Parts

In Week 1 we broke a URL into its parts. The `location` object (short for `window.location`) gives JavaScript each of those parts for the current page. Take this example URL for a site deployed to GitHub Pages, as in Week 7:

`https://student.github.io/trailhead/tour.html?tour=ridge&group=4#pricing`

| Property | Value | Meaning |
| --- | --- | --- |
| `location.href` | `https://student.github.io/trailhead/tour.html?tour=ridge&group=4#pricing` | The full URL |
| `location.protocol` | `https:` | The protocol, including the colon |
| `location.host` | `student.github.io` | Host name plus port, if any |
| `location.hostname` | `student.github.io` | Host name only |
| `location.port` | `""` | Empty when the default port is used |
| `location.pathname` | `/trailhead/tour.html` | The path to the file |
| `location.search` | `?tour=ridge&group=4` | The query string, including `?` |
| `location.hash` | `#pricing` | The fragment, including `#` |
| `location.origin` | `https://student.github.io` | Protocol, host, and port together |

`host` and `hostname` only differ when a port is part of the URL. On a local development server such as VS Code's Live Server, the URL might be `http://127.0.0.1:5500/tour.html`. In that case `location.host` is `127.0.0.1:5500`, `location.hostname` is `127.0.0.1`, and `location.port` is `5500`.

Try this in the Console on any web page:

```js
console.log(location.href);
console.log(location.protocol);
console.log(location.host);
console.log(location.pathname);
console.log(location.search);
console.log(location.hash);
```

### Reading Query String Values with URLSearchParams

The query string is the most useful part for our scripts. It lets one page behave differently depending on the link that opened it. `location.search` gives us the whole string (`?tour=ridge&group=4`). The built-in `URLSearchParams` object reads the individual values out of it, so we don't have to cut up the string by hand:

```js
// Current URL: tour.html?tour=ridge&group=4
const params = new URLSearchParams(location.search);

const tourKey = params.get("tour");    // "ridge"
const group = params.get("group");     // "4" (a string, not a number)
const coupon = params.get("coupon");   // null, because it is not in the URL

console.log(params.has("tour"));       // true
```

Two habits from earlier weeks apply directly here:

- Every value from a query string is a string. Convert it with `Number()` before doing math, and check it with `isNaN()` (Week 8).
- `get()` returns `null` when a value is missing, and users can type anything into an address bar. Always guard against missing or unexpected values (Week 5).

### Redirecting to Another Page

Assigning a new URL to `location.href` sends the browser to that page:

```js
location.href = "register.html";
```

Relative URLs work exactly as they did in HTML links. They are resolved relative to the current page, which matters once a site is deployed in a repository subfolder (Week 7). The `location` object has three related methods:

| Method | What it does |
| --- | --- |
| `location.assign(url)` | Same as setting `location.href`. Adds a new entry to the tab's history. |
| `location.replace(url)` | Loads the new page in place of the current one. The current page is removed from history, so the Back button skips it. |
| `location.reload()` | Reloads the current page. |

Combining a redirect with `confirm()`:

```js
const registerBtn = document.querySelector("#register-btn");

registerBtn.addEventListener("click", () => {
  if (confirm("Go to the registration page?")) {
    location.href = "register.html";
  }
});
```

Setting `location.search` to a new query string also reloads the page with that query string. Setting `location.hash` does not reload the page, as the next section shows.

### Working with the Hash

Setting `location.hash` jumps to the element with that `id`, just like clicking a link to `#pricing`. The page is not reloaded. When the hash changes, `window` fires a `hashchange` event:

```js
window.addEventListener("hashchange", () => {
  console.log(`Now viewing section: ${location.hash}`);
});

location.hash = "#pricing"; // scrolls to the element with id="pricing"
```

## The history Object

### What history Can and Cannot Do

Each browser tab keeps a list of the pages visited in it. That list is what the Back and Forward buttons move through. The `history` object lets JavaScript move through the list too.

For privacy reasons, JavaScript cannot read the URLs in that list. If it could, any website could see where you had been. The only thing you can read is how many entries there are:

```js
console.log(history.length); // number of entries in this tab's history
```

### back(), forward(), and go()

| Method | Same as |
| --- | --- |
| `history.back()` | Clicking the browser's Back button |
| `history.forward()` | Clicking the browser's Forward button |
| `history.go(-2)` | Clicking Back twice |
| `history.go(1)` | Same as `forward()` |
| `history.go(0)` | Reloading the page |

```js
const backBtn = document.querySelector("#back-btn");

backBtn.addEventListener("click", () => {
  history.back();
});
```

If there is no previous page, for example in a brand new tab, `history.back()` silently does nothing. No error is thrown. `history.length` includes forward entries too, so it cannot tell you for certain whether going back is possible. When it matters, give users a normal link to your home page as well.

### How location.href and location.replace() Affect History

Here is a visit that uses both kinds of redirect:

1. The user opens `index.html`. History: `index.html`
2. A script runs `location.href = "tour.html"`. History: `index.html`, `tour.html`
3. A script runs `location.replace("register.html")`. History: `index.html`, `register.html`

At step 3, `tour.html` was replaced rather than kept, so pressing Back on the registration page goes straight to `index.html`. Use `replace()` when the current page should not be revisited, such as an error page that redirects on its own.

Modern browsers also provide `history.pushState()` and `history.replaceState()`, which change the URL without loading a new page. They are mostly used in single-page application frameworks and are beyond the scope of this course.

## Exercise: Trailhead Tour Details Page

Trailhead Adventure Tours (from Week 8) wants a details page for each of its tours. Instead of writing a separate HTML page for every tour, the site will have one `tour.html` page. It reads the tour name from the query string and fills itself in. You will also add Back and Forward buttons, a page-info panel showing the parts of the current URL, and a Register button that redirects to a registration page.

Run this exercise through a local development server (such as VS Code's Live Server extension) or from your GitHub Pages site from Week 7. Opening the files directly by double-clicking mostly works, but `location.protocol` will show `file:` and the host will be empty, which makes the page-info panel less interesting.

Create a folder with these files. You will write `tour.js`; the rest are given.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Trailhead Adventure Tours</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Trailhead Adventure Tours</h1>
    <p>Choose a tour to see the details.</p>
  </header>
  <main>
    <ul class="tour-list">
      <li><a href="tour.html?tour=lakeshore">Lakeshore Loop</a></li>
      <li><a href="tour.html?tour=ridge">Ridge Line Trek</a></li>
      <li><a href="tour.html?tour=canyon">Canyon Explorer</a></li>
      <li><a href="tour.html?tour=summit">Summit Sunrise</a></li>
    </ul>
  </main>
</body>
</html>
```

The Summit Sunrise link is intentional: that tour does not exist in our data yet. It is there so you can test what happens when the query string contains an unexpected value.

Given: `tour.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tour Details | Trailhead Adventure Tours</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Trailhead Adventure Tours</h1>
  </header>
  <main>
    <section class="card">
      <h2 id="tour-name">Loading tour...</h2>
      <p id="tour-description"></p>
      <p id="tour-price"></p>
      <div class="buttons">
        <button id="back-btn">Back</button>
        <button id="forward-btn">Forward</button>
        <button id="register-btn">Register for This Tour</button>
      </div>
    </section>
    <section class="card">
      <h2>Page Info</h2>
      <ul class="page-info">
        <li>Protocol: <span id="info-protocol"></span></li>
        <li>Host: <span id="info-host"></span></li>
        <li>Path: <span id="info-path"></span></li>
        <li>Query string: <span id="info-search"></span></li>
        <li>History entries in this tab: <span id="info-history"></span></li>
      </ul>
    </section>
  </main>
  <script src="tour.js"></script>
</body>
</html>
```

Given: `register.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Register | Trailhead Adventure Tours</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Trailhead Adventure Tours</h1>
  </header>
  <main>
    <section class="card">
      <h2>Registration</h2>
      <p>The registration form from Week 8 would go here. Look at the address bar: the tour you picked came along in the query string.</p>
      <p><a href="index.html">Back to all tours</a></p>
    </section>
  </main>
</body>
</html>
```

Given: `styles.css`

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  background: #f3f6f1;
  color: #2b2b2b;
}

header {
  background: #2f5d3a;
  color: #ffffff;
  padding: 1.5rem 1rem;
  text-align: center;
}

header h1 {
  margin: 0;
}

header p {
  margin: 0.5rem 0 0;
}

main {
  max-width: 720px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.card {
  background: #ffffff;
  border-radius: 8px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.tour-list {
  list-style: none;
  padding: 0;
}

.tour-list li {
  margin-bottom: 0.75rem;
}

.tour-list a {
  display: block;
  background: #ffffff;
  padding: 1rem;
  border-radius: 8px;
  color: #2f5d3a;
  font-weight: bold;
  text-decoration: none;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.tour-list a:hover {
  background: #e4eee2;
}

.buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

button {
  padding: 0.6rem 1rem;
  border: none;
  border-radius: 6px;
  background: #2f5d3a;
  color: #ffffff;
  font-size: 1rem;
  cursor: pointer;
}

button:hover {
  background: #24492d;
}

button:disabled {
  background: #9aa79c;
  cursor: not-allowed;
}

.page-info {
  padding-left: 1.2rem;
}

.error {
  color: #b3261e;
}
```

### Step 1: Store the Tour Data

Each tour is an object, and all the tours are stored in one `tours` object (Week 3). The keys (`lakeshore`, `ridge`, `canyon`) match the values used in the query strings in `index.html`, which is what lets us look a tour up by its key.

```js
// tour.js
const tours = {
  lakeshore: {
    name: "Lakeshore Loop",
    description: "An easy 5 km walk along the shoreline with two lookout points.",
    price: 35
  },
  ridge: {
    name: "Ridge Line Trek",
    description: "A moderate 12 km hike along the ridge with views over the whole valley.",
    price: 60
  },
  canyon: {
    name: "Canyon Explorer",
    description: "A challenging 18 km route down into the canyon and back up again.",
    price: 85
  }
};
```

Checkpoint: temporarily add `console.log(tours.ridge.name);` at the bottom of the file, open `tour.html`, and confirm the Console shows `Ridge Line Trek`. Then remove the line.

### Step 2: Read the Tour Key from the URL

```js
const params = new URLSearchParams(location.search);
const tourKey = params.get("tour");

console.log(`Tour key from URL: ${tourKey}`);
```

Checkpoint: open `index.html` and click Ridge Line Trek. The Console should show `Tour key from URL: ridge`. Now delete `?tour=ridge` from the address bar and press Enter. The Console should show `null`.

### Step 3: Display the Tour, with a Guard Clause

Bracket notation (Week 3) looks up the tour using the key from the URL. If the key is missing or not in our data, `tours[tourKey]` is `undefined`. A guard clause (Week 5) shows a friendly message and stops the function before it tries to read properties that don't exist.

```js
const nameEl = document.querySelector("#tour-name");
const descriptionEl = document.querySelector("#tour-description");
const priceEl = document.querySelector("#tour-price");
const registerBtn = document.querySelector("#register-btn");

function showTour() {
  const tour = tours[tourKey];

  if (!tour) {
    nameEl.textContent = "Tour not found";
    nameEl.classList.add("error");
    descriptionEl.textContent = "Please go back and choose a tour from the list.";
    registerBtn.disabled = true;
    return;
  }

  const formattedPrice = tour.price.toLocaleString("en-US", {
    style: "currency",
    currency: "USD"
  });

  nameEl.textContent = tour.name;
  descriptionEl.textContent = tour.description;
  priceEl.textContent = `Price per hiker: ${formattedPrice}`;
  document.title = `${tour.name} | Trailhead Adventure Tours`;
}

showTour();
```

Two small new pieces here. `document.title` changes the text shown on the browser tab. Setting a button's `disabled` property to `true` stops it from being clicked, and the given CSS greys it out.

Checkpoint: click Ridge Line Trek. The card should show the tour's name, description, and price as `$60.00`, and the browser tab should read `Ridge Line Trek | Trailhead Adventure Tours`. Go back and click Summit Sunrise. You should see "Tour not found" in red with the Register button greyed out, and no errors in the Console.

### Step 4: Show the Page Info

```js
function showPageInfo() {
  document.querySelector("#info-protocol").textContent = location.protocol;
  document.querySelector("#info-host").textContent = location.host;
  document.querySelector("#info-path").textContent = location.pathname;
  document.querySelector("#info-history").textContent = history.length;

  const searchEl = document.querySelector("#info-search");
  if (location.search === "") {
    searchEl.textContent = "(none)";
  } else {
    searchEl.textContent = location.search;
  }
}

showPageInfo();
```

Checkpoint: on Live Server you should see a protocol of `http:` and a host like `127.0.0.1:5500`. On GitHub Pages you should see `https:`, your `username.github.io` host, and a path that includes your repository name (Week 7). Click through a few tours and watch the history count grow.

### Step 5: Add Back and Forward Buttons

```js
document.querySelector("#back-btn").addEventListener("click", () => {
  history.back();
});

document.querySelector("#forward-btn").addEventListener("click", () => {
  history.forward();
});
```

Checkpoint: from `index.html`, open Lakeshore Loop and click your Back button. You should return to the tour list. Now click the browser's own Forward arrow and you should be back on Lakeshore Loop. Your Back and Forward buttons and the browser's arrows move through the same history list.

### Step 6: Redirect to Registration

The Register button asks for confirmation, then redirects to `register.html`. The tour key goes along in the query string so the registration page could know which tour was chosen.

```js
registerBtn.addEventListener("click", () => {
  const tour = tours[tourKey];
  const goToForm = confirm(`Register for the ${tour.name}?`);

  if (goToForm) {
    location.href = `register.html?tour=${tourKey}`;
  }
});
```

We don't need to guard against a missing tour here because Step 3 disables this button when the tour is not found.

Checkpoint: open Canyon Explorer and click Register. Click Cancel and you should stay on the page. Click Register again and choose OK. The address bar should end in `register.html?tour=canyon`. Press the browser's Back button and you should return to Canyon Explorer.

### Solution

```js
// tour.js
const tours = {
  lakeshore: {
    name: "Lakeshore Loop",
    description: "An easy 5 km walk along the shoreline with two lookout points.",
    price: 35
  },
  ridge: {
    name: "Ridge Line Trek",
    description: "A moderate 12 km hike along the ridge with views over the whole valley.",
    price: 60
  },
  canyon: {
    name: "Canyon Explorer",
    description: "A challenging 18 km route down into the canyon and back up again.",
    price: 85
  }
};

const params = new URLSearchParams(location.search);
const tourKey = params.get("tour");

const nameEl = document.querySelector("#tour-name");
const descriptionEl = document.querySelector("#tour-description");
const priceEl = document.querySelector("#tour-price");
const registerBtn = document.querySelector("#register-btn");

function showTour() {
  const tour = tours[tourKey];

  if (!tour) {
    nameEl.textContent = "Tour not found";
    nameEl.classList.add("error");
    descriptionEl.textContent = "Please go back and choose a tour from the list.";
    registerBtn.disabled = true;
    return;
  }

  const formattedPrice = tour.price.toLocaleString("en-US", {
    style: "currency",
    currency: "USD"
  });

  nameEl.textContent = tour.name;
  descriptionEl.textContent = tour.description;
  priceEl.textContent = `Price per hiker: ${formattedPrice}`;
  document.title = `${tour.name} | Trailhead Adventure Tours`;
}

function showPageInfo() {
  document.querySelector("#info-protocol").textContent = location.protocol;
  document.querySelector("#info-host").textContent = location.host;
  document.querySelector("#info-path").textContent = location.pathname;
  document.querySelector("#info-history").textContent = history.length;

  const searchEl = document.querySelector("#info-search");
  if (location.search === "") {
    searchEl.textContent = "(none)";
  } else {
    searchEl.textContent = location.search;
  }
}

document.querySelector("#back-btn").addEventListener("click", () => {
  history.back();
});

document.querySelector("#forward-btn").addEventListener("click", () => {
  history.forward();
});

registerBtn.addEventListener("click", () => {
  const tour = tours[tourKey];
  const goToForm = confirm(`Register for the ${tour.name}?`);

  if (goToForm) {
    location.href = `register.html?tour=${tourKey}`;
  }
});

showTour();
showPageInfo();
```

**Challenge:** Make two improvements to the tour page.

1. Add a Next Tour button that moves to the next tour in the order Lakeshore Loop, Ridge Line Trek, Canyon Explorer, then back to Lakeshore Loop. Don't use an array. Instead, give each tour object a `next` property holding the key of the tour that follows it. The button should navigate with `location.href`, so each tour you visit is added to history and your Back button walks back through them.
2. When a tour is not found, send the user back to the tour list automatically after three seconds using `setTimeout()` (Week 6). Use `location.replace()` rather than `location.href`, so the broken page is removed from history and pressing Back does not land on it again. The Next Tour button should also be disabled in this case.

Add the new button to `tour.html`, inside the existing `.buttons` div:

```html
<div class="buttons">
  <button id="back-btn">Back</button>
  <button id="forward-btn">Forward</button>
  <button id="next-btn">Next Tour</button>
  <button id="register-btn">Register for This Tour</button>
</div>
```

Updated `tour.js`:

```js
// tour.js
const tours = {
  lakeshore: {
    name: "Lakeshore Loop",
    description: "An easy 5 km walk along the shoreline with two lookout points.",
    price: 35,
    next: "ridge"
  },
  ridge: {
    name: "Ridge Line Trek",
    description: "A moderate 12 km hike along the ridge with views over the whole valley.",
    price: 60,
    next: "canyon"
  },
  canyon: {
    name: "Canyon Explorer",
    description: "A challenging 18 km route down into the canyon and back up again.",
    price: 85,
    next: "lakeshore"
  }
};

const params = new URLSearchParams(location.search);
const tourKey = params.get("tour");

const nameEl = document.querySelector("#tour-name");
const descriptionEl = document.querySelector("#tour-description");
const priceEl = document.querySelector("#tour-price");
const registerBtn = document.querySelector("#register-btn");
const nextBtn = document.querySelector("#next-btn");

function showTour() {
  const tour = tours[tourKey];

  if (!tour) {
    nameEl.textContent = "Tour not found";
    nameEl.classList.add("error");
    descriptionEl.textContent = "Taking you back to the tour list in 3 seconds...";
    registerBtn.disabled = true;
    nextBtn.disabled = true;

    setTimeout(() => {
      location.replace("index.html");
    }, 3000);
    return;
  }

  const formattedPrice = tour.price.toLocaleString("en-US", {
    style: "currency",
    currency: "USD"
  });

  nameEl.textContent = tour.name;
  descriptionEl.textContent = tour.description;
  priceEl.textContent = `Price per hiker: ${formattedPrice}`;
  document.title = `${tour.name} | Trailhead Adventure Tours`;
}

function showPageInfo() {
  document.querySelector("#info-protocol").textContent = location.protocol;
  document.querySelector("#info-host").textContent = location.host;
  document.querySelector("#info-path").textContent = location.pathname;
  document.querySelector("#info-history").textContent = history.length;

  const searchEl = document.querySelector("#info-search");
  if (location.search === "") {
    searchEl.textContent = "(none)";
  } else {
    searchEl.textContent = location.search;
  }
}

document.querySelector("#back-btn").addEventListener("click", () => {
  history.back();
});

document.querySelector("#forward-btn").addEventListener("click", () => {
  history.forward();
});

nextBtn.addEventListener("click", () => {
  const tour = tours[tourKey];
  location.href = `tour.html?tour=${tour.next}`;
});

registerBtn.addEventListener("click", () => {
  const tour = tours[tourKey];
  const goToForm = confirm(`Register for the ${tour.name}?`);

  if (goToForm) {
    location.href = `register.html?tour=${tourKey}`;
  }
});

showTour();
showPageInfo();
```

To test, open Lakeshore Loop and click Next Tour three times. You should go through Ridge Line Trek and Canyon Explorer and arrive back at Lakeshore Loop, with the history count rising each time. Your Back button should then walk back through the same tours in reverse. Next, click Summit Sunrise from the tour list. After three seconds you should be returned to the list. Press the browser's Back button and notice it does not return you to the "Tour not found" page.

## The navigator Object

The `navigator` object describes the browser and the environment it is running in.

### Browser and Language Information

```js
console.log(navigator.userAgent);     // a long string identifying the browser
console.log(navigator.language);      // the user's preferred language, e.g. "en-US"
console.log(navigator.cookieEnabled); // true or false
```

`navigator.userAgent` looks tempting for checking which browser someone is using, but it is unreliable. For historical reasons, almost every browser's user agent string claims to be several other browsers too, and users can change it. To find out whether a browser supports a feature, check for the feature itself. This is called feature detection:

```js
if (navigator.geolocation) {
  console.log("This browser can provide location information.");
} else {
  console.log("Location information is not available in this browser.");
}
```

The geolocation feature itself asks the user for permission and only works on secure (`https:`) pages such as GitHub Pages. We use it here only as an example of feature detection.

`navigator.language` pairs well with the `toLocaleString()` formatting from Week 8. It lets you format numbers and dates the way the user is used to seeing them:

```js
const price = 1234.5;

console.log(price.toLocaleString(navigator.language, {
  style: "currency",
  currency: "USD"
}));
// "$1,234.50" for en-US, "1.234,50 $" for de-DE, and so on
```

### Online and Offline Status

`navigator.onLine` is `true` when the browser has a network connection and `false` when it does not. When the connection changes, `window` fires an `online` or `offline` event:

```html
<p>Connection: <span id="connection"></span></p>
```

```js
const connectionEl = document.querySelector("#connection");

function showConnection() {
  if (navigator.onLine) {
    connectionEl.textContent = "Online";
  } else {
    connectionEl.textContent = "Offline";
  }
}

window.addEventListener("online", showConnection);
window.addEventListener("offline", showConnection);
showConnection();
```

`true` only means the device is connected to a network, not that the internet is actually reachable. To test this without unplugging anything, open DevTools, go to the Network panel, and change the throttling dropdown (usually labeled "No throttling") to "Offline".

## The screen Object

The `screen` object describes the user's physical display, regardless of how big the browser window is.

```js
console.log(`Screen: ${screen.width} x ${screen.height}`);
console.log(`Available: ${screen.availWidth} x ${screen.availHeight}`);
console.log(`Color depth: ${screen.colorDepth} bits`);
console.log(`Pixel ratio: ${window.devicePixelRatio}`);
```

It is easy to mix up the `screen` and `window` size properties:

| Property | What it measures | Changes when you resize the browser? |
| --- | --- | --- |
| `screen.width`, `screen.height` | The entire display | No |
| `screen.availWidth`, `screen.availHeight` | The display minus the operating system's taskbar or dock | No |
| `window.outerWidth`, `window.outerHeight` | The browser window, including toolbars | Yes |
| `window.innerWidth`, `window.innerHeight` | The viewport where the page is drawn | Yes |

`window.devicePixelRatio` tells you how many physical pixels make up one CSS pixel. It is often 2 or 3 on phones and high-resolution laptops.

To change a page's layout for different screen sizes, CSS media queries are still the right tool. `screen` is more useful for information panels, or for deciding things like which size of image to preload (Week 6).

DevTools can pretend to be a phone. Click the device toolbar icon (a phone and tablet) at the top of DevTools, pick a device, reload, and run the code above again to see the values change.

## Web Storage: localStorage and sessionStorage

### Why Store Data in the Browser

Every variable in our scripts is lost the moment the page reloads. Web Storage lets a page save small pieces of text in the browser as key-value pairs and read them back later. Common uses include remembering a user's name, a theme preference, or a half-finished form.

Storage belongs to an origin, which is the protocol, host, and port together (`location.origin`). A page can only see the storage saved by pages from the same origin. This has two practical consequences:

- Your Live Server copy (`http://127.0.0.1:5500`) and your GitHub Pages copy (`https://username.github.io`) are different origins, so they have separate storage.
- All of your GitHub Pages project sites share one origin, `https://username.github.io`, so they share one storage area. If two of your projects both save a key called `name`, they will overwrite each other. Prefix your keys with the project name, such as `trailhead-name`.

### localStorage vs sessionStorage

Both objects have exactly the same methods. They differ only in how long the data lasts and who can see it:

| | `localStorage` | `sessionStorage` |
| --- | --- | --- |
| How long data lasts | Until your script or the user deletes it, even after the browser is closed | Until the tab is closed. It survives reloads. |
| Shared between tabs? | Yes, every tab from the same origin | No, each tab has its own |
| Typical size limit | About 5 MB per origin | About 5 MB per origin |
| Good for | Preferences, remembered names, visit counts | Draft text, progress through a multi-step form |

### Saving, Reading, and Removing Data

| Method | What it does |
| --- | --- |
| `setItem(key, value)` | Saves a value under a key, replacing any existing value |
| `getItem(key)` | Returns the value for a key, or `null` if there is none |
| `removeItem(key)` | Deletes one key |
| `clear()` | Deletes every key for this origin |

```js
localStorage.setItem("trailhead-name", "Maya");

const savedName = localStorage.getItem("trailhead-name");
console.log(savedName); // "Maya"

localStorage.removeItem("trailhead-name");
console.log(localStorage.getItem("trailhead-name")); // null
```

Reload the page after `setItem()` and comment out the other lines. `getItem()` will still return `"Maya"`. Swap `localStorage` for `sessionStorage` and the value survives a reload but disappears once the tab is closed.

Be careful with `clear()`. On GitHub Pages it would also wipe the saved data of every other project you host there. Removing your own keys one at a time with `removeItem()` is safer.

### Everything Is Stored as a String

Web Storage can only hold strings. Anything else you save is converted to a string first, and you get a string back:

```js
localStorage.setItem("trailhead-visits", 3);

const visits = localStorage.getItem("trailhead-visits");
console.log(typeof visits);       // "string"
console.log(visits + 1);          // "31", string joining, not addition
console.log(Number(visits) + 1);  // 4
```

Booleans have the same problem, and it's easy to miss. The string `"false"` is not empty, so JavaScript treats it as true in an `if` statement:

```js
localStorage.setItem("trailhead-dark-theme", false);

const darkTheme = localStorage.getItem("trailhead-dark-theme"); // "false"

if (darkTheme) {
  console.log("This runs, because the string \"false\" is not empty.");
}

if (darkTheme === "true") {
  console.log("This is the correct check, and it does not run.");
}
```

Objects don't survive at all. `localStorage.setItem("trailhead-hiker", { name: "Maya" })` saves the useless string `"[object Object]"`. Storing objects and lists needs a format called JSON, which we will meet in Week 10.

A reliable pattern for reading a stored number combines a `null` check with the `isNaN()` check from Week 8:

```js
const stored = localStorage.getItem("trailhead-visits");
let visits = 0;

if (stored !== null && !isNaN(stored)) {
  visits = Number(stored);
}

visits = visits + 1;
localStorage.setItem("trailhead-visits", visits);
console.log(`Visit number ${visits}`);
```

### When Storage Is Not Available

Storage can fail. Some privacy settings block it entirely, and saving can fail once the size limit is reached. In those cases the storage methods throw an error, so wrap storage code in `try...catch` (Week 5). That way a blocked storage area doesn't stop the rest of the page from working:

```js
function saveSetting(key, value) {
  try {
    localStorage.setItem(key, value);
  } catch (error) {
    console.warn(`Could not save ${key}: ${error.message}`);
  }
}
```

### Inspecting Storage in DevTools

In Chrome or Edge, open DevTools and go to the Application panel. In the sidebar, expand Local storage or Session storage and click your site's origin. You will see every key and value, and you can edit or delete them by double-clicking or right-clicking. Firefox shows the same information in its Storage panel. This is the quickest way to check what your script has actually saved.

### What Not to Store

Web Storage is not encrypted. Any script running on the page can read it, and anyone using the computer can see it in DevTools. Never store passwords, payment details, or anything else sensitive in `localStorage` or `sessionStorage`. We will come back to this in Week 14 when we look at security.

## Exercise: Trailhead Trip Planner

Trailhead Adventure Tours wants a Trip Planner page for returning hikers. It should:

- greet the hiker by name and count how many times they have opened the planner, using `localStorage`
- keep the hiker's trip notes through a reload, but not after the tab is closed, using `sessionStorage`
- show a small panel of device information using `navigator`, `screen`, and `window`, updating live when the connection changes or the window is resized

Add these files to the same folder as the Tour Details exercise. Run the page through Live Server or GitHub Pages, as before.

Given: `planner.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Trip Planner | Trailhead Adventure Tours</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Trailhead Adventure Tours</h1>
    <p>Your Trip Planner</p>
  </header>
  <main>
    <section class="card">
      <h2 id="welcome">Welcome, hiker!</h2>
      <p id="visit-count"></p>
      <label for="name-input">Your first name</label>
      <input type="text" id="name-input">
      <div class="buttons">
        <button id="save-name-btn">Remember Me</button>
        <button id="forget-btn">Forget Me</button>
      </div>
    </section>
    <section class="card">
      <h2>Trip Notes</h2>
      <p class="hint">Your notes are kept until you close this tab.</p>
      <textarea id="notes" rows="5"></textarea>
    </section>
    <section class="card">
      <h2>Your Device</h2>
      <ul class="page-info">
        <li>Language: <span id="info-language"></span></li>
        <li>Connection: <span id="info-connection"></span></li>
        <li>Screen: <span id="info-screen"></span></li>
        <li>Browser viewport: <span id="info-viewport"></span></li>
      </ul>
    </section>
    <p><a href="index.html">Back to all tours</a></p>
  </main>
  <script src="planner.js"></script>
</body>
</html>
```

Given: add these rules to the bottom of `styles.css`

```css
/* Trip Planner additions */
label {
  display: block;
  margin-bottom: 0.25rem;
}

input[type="text"],
textarea {
  width: 100%;
  padding: 0.6rem;
  margin-bottom: 1rem;
  border: 1px solid #b7c4b9;
  border-radius: 6px;
  font-size: 1rem;
  font-family: inherit;
}

.hint {
  color: #5c665e;
  font-size: 0.9rem;
}

.offline {
  color: #b3261e;
}
```

### Step 1: Define the Storage Keys

Storing each key name in a constant means a typo shows up as a `ReferenceError` in the Console, instead of silently saving to the wrong key. The `trailhead-` prefix keeps our keys from clashing with other projects on the same GitHub Pages origin.

```js
// planner.js
const nameKey = "trailhead-name";
const visitsKey = "trailhead-visits";
const notesKey = "trailhead-notes";
```

### Step 2: Count Visits

This uses the stored-number pattern from the theory section.

```js
const visitEl = document.querySelector("#visit-count");

function countVisit() {
  const stored = localStorage.getItem(visitsKey);
  let visits = 0;

  if (stored !== null && !isNaN(stored)) {
    visits = Number(stored);
  }

  visits = visits + 1;
  localStorage.setItem(visitsKey, visits);
  visitEl.textContent = `You have opened the planner ${visits} time(s) in this browser.`;
}

countVisit();
```

Checkpoint: open `planner.html` and reload it a few times. The count should go up each time. Open the Application panel in DevTools and find `trailhead-visits` under Local storage. Close the browser completely, reopen the page, and the count should carry on from where it was.

### Step 3: Remember the Hiker's Name

```js
const welcomeEl = document.querySelector("#welcome");
const nameInput = document.querySelector("#name-input");

function showWelcome() {
  const savedName = localStorage.getItem(nameKey);

  if (savedName === null) {
    welcomeEl.textContent = "Welcome, hiker!";
  } else {
    welcomeEl.textContent = `Welcome back, ${savedName}!`;
  }
}

document.querySelector("#save-name-btn").addEventListener("click", () => {
  const name = nameInput.value.trim();

  if (name === "") {
    alert("Please enter your name first.");
    return;
  }

  localStorage.setItem(nameKey, name);
  nameInput.value = "";
  showWelcome();
});

showWelcome();
```

Checkpoint: enter a name and click Remember Me. The heading should change to "Welcome back, [your name]!" Reload the page and the greeting should still be there. Click Remember Me with an empty box and you should get the alert, with nothing saved.

### Step 4: Forget the Hiker

The Forget Me button removes only this page's keys, using `removeItem()` rather than `clear()`, for the shared-origin reason covered earlier.

```js
document.querySelector("#forget-btn").addEventListener("click", () => {
  localStorage.removeItem(nameKey);
  localStorage.removeItem(visitsKey);
  showWelcome();
  visitEl.textContent = "Your saved details have been removed from this browser.";
});
```

Checkpoint: click Forget Me. The heading should return to "Welcome, hiker!" and both keys should disappear from the Application panel (you may need to click its refresh button). Reload the page and the visit count should start again at 1.

### Step 5: Keep Trip Notes for This Tab

The `input` event fires every time the content of a text box or textarea changes, whether by typing, pasting, or deleting. Saving on every `input` event means the notes are always up to date in `sessionStorage`.

```js
const notesEl = document.querySelector("#notes");

function loadNotes() {
  const savedNotes = sessionStorage.getItem(notesKey);

  if (savedNotes !== null) {
    notesEl.value = savedNotes;
  }
}

notesEl.addEventListener("input", () => {
  sessionStorage.setItem(notesKey, notesEl.value);
});

loadNotes();
```

Checkpoint: type a few notes and reload the page. The notes should still be there. Now open a new tab, type the planner's address into it yourself, and notice that the notes box is empty there, because each tab has its own `sessionStorage`. Finally, close the original tab, reopen the planner, and the notes should be gone. (Use a new tab rather than the browser's "Duplicate tab" option, which copies the session storage across.)

### Step 6: Show Device Information

```js
const connectionEl = document.querySelector("#info-connection");
const viewportEl = document.querySelector("#info-viewport");

function showDeviceInfo() {
  document.querySelector("#info-language").textContent = navigator.language;
  document.querySelector("#info-screen").textContent = `${screen.width} x ${screen.height} pixels`;
}

function showConnection() {
  if (navigator.onLine) {
    connectionEl.textContent = "Online";
    connectionEl.classList.remove("offline");
  } else {
    connectionEl.textContent = "Offline";
    connectionEl.classList.add("offline");
  }
}

function showViewport() {
  viewportEl.textContent = `${window.innerWidth} x ${window.innerHeight} pixels`;
}

window.addEventListener("online", showConnection);
window.addEventListener("offline", showConnection);
window.addEventListener("resize", showViewport);

showDeviceInfo();
showConnection();
showViewport();
```

Checkpoint: resize the browser and the viewport size should update while the screen size stays the same. In DevTools, set Network throttling to Offline and the connection should switch to a red "Offline". Set it back to No throttling and it should return to "Online".

### Step 7: Protect the Page When Storage Is Blocked

If storage is blocked, the first `getItem()` call throws an error. That would stop the rest of the script, including the device panel, which doesn't need storage at all. Wrapping the storage calls that run on page load in `try...catch` keeps the rest of the page working. Replace the separate `countVisit();`, `showWelcome();`, and `loadNotes();` calls from Steps 2, 3, and 5 with one protected block at the end of the file:

```js
try {
  countVisit();
  showWelcome();
  loadNotes();
} catch (error) {
  console.error(`Storage is not available: ${error.message}`);
  visitEl.textContent = "Saving is turned off in this browser, so the planner cannot remember you.";
}
```

Checkpoint: the page should behave exactly as before. To see the `catch` block run, temporarily change `countVisit();` to `countVisitt();` and reload. You get a `ReferenceError` rather than a storage error, but it is caught the same way. Undo the typo afterwards.

### Solution

```js
// planner.js
const nameKey = "trailhead-name";
const visitsKey = "trailhead-visits";
const notesKey = "trailhead-notes";

const visitEl = document.querySelector("#visit-count");
const welcomeEl = document.querySelector("#welcome");
const nameInput = document.querySelector("#name-input");
const notesEl = document.querySelector("#notes");
const connectionEl = document.querySelector("#info-connection");
const viewportEl = document.querySelector("#info-viewport");

function countVisit() {
  const stored = localStorage.getItem(visitsKey);
  let visits = 0;

  if (stored !== null && !isNaN(stored)) {
    visits = Number(stored);
  }

  visits = visits + 1;
  localStorage.setItem(visitsKey, visits);
  visitEl.textContent = `You have opened the planner ${visits} time(s) in this browser.`;
}

function showWelcome() {
  const savedName = localStorage.getItem(nameKey);

  if (savedName === null) {
    welcomeEl.textContent = "Welcome, hiker!";
  } else {
    welcomeEl.textContent = `Welcome back, ${savedName}!`;
  }
}

function loadNotes() {
  const savedNotes = sessionStorage.getItem(notesKey);

  if (savedNotes !== null) {
    notesEl.value = savedNotes;
  }
}

function showDeviceInfo() {
  document.querySelector("#info-language").textContent = navigator.language;
  document.querySelector("#info-screen").textContent = `${screen.width} x ${screen.height} pixels`;
}

function showConnection() {
  if (navigator.onLine) {
    connectionEl.textContent = "Online";
    connectionEl.classList.remove("offline");
  } else {
    connectionEl.textContent = "Offline";
    connectionEl.classList.add("offline");
  }
}

function showViewport() {
  viewportEl.textContent = `${window.innerWidth} x ${window.innerHeight} pixels`;
}

document.querySelector("#save-name-btn").addEventListener("click", () => {
  const name = nameInput.value.trim();

  if (name === "") {
    alert("Please enter your name first.");
    return;
  }

  localStorage.setItem(nameKey, name);
  nameInput.value = "";
  showWelcome();
});

document.querySelector("#forget-btn").addEventListener("click", () => {
  localStorage.removeItem(nameKey);
  localStorage.removeItem(visitsKey);
  showWelcome();
  visitEl.textContent = "Your saved details have been removed from this browser.";
});

notesEl.addEventListener("input", () => {
  sessionStorage.setItem(notesKey, notesEl.value);
});

window.addEventListener("online", showConnection);
window.addEventListener("offline", showConnection);
window.addEventListener("resize", showViewport);

try {
  countVisit();
  showWelcome();
  loadNotes();
} catch (error) {
  console.error(`Storage is not available: ${error.message}`);
  visitEl.textContent = "Saving is turned off in this browser, so the planner cannot remember you.";
}

showDeviceInfo();
showConnection();
showViewport();
```

**Challenge:** Add a theme button to the header that switches the planner between a light and a dark theme. The choice should be remembered in `localStorage` under the key `trailhead-theme`, so the planner opens in the same theme next time. Save the strings `"dark"` and `"light"` rather than `true` and `false`, to avoid the `"false"` problem from the theory section. Apply the theme by adding or removing a `dark` class on `document.body` with `classList` (Week 4). You will need `classList.contains("dark")`, which returns `true` if the element currently has that class.

Add the button inside the `<header>` in `planner.html`, after the paragraph:

```html
<header>
  <h1>Trailhead Adventure Tours</h1>
  <p>Your Trip Planner</p>
  <button id="theme-btn" class="theme-btn">Switch to Dark Theme</button>
</header>
```

Add these rules to the bottom of `styles.css`:

```css
/* Trip Planner theme */
.theme-btn {
  margin-top: 0.75rem;
  background: #ffffff;
  color: #2f5d3a;
}

.theme-btn:hover {
  background: #e4eee2;
}

body.dark {
  background: #1e2420;
  color: #e8ede9;
}

body.dark .card {
  background: #2b332d;
}

body.dark .hint {
  color: #b7c4b9;
}

body.dark a {
  color: #9fd3a8;
}

body.dark input[type="text"],
body.dark textarea {
  background: #1e2420;
  color: #e8ede9;
  border-color: #4a574d;
}
```

Add this code to `planner.js`. The new constant and function go with the others near the top, and the click listener goes with the other listeners:

```js
const themeKey = "trailhead-theme";
const themeBtn = document.querySelector("#theme-btn");

function applyTheme(theme) {
  if (theme === "dark") {
    document.body.classList.add("dark");
    themeBtn.textContent = "Switch to Light Theme";
  } else {
    document.body.classList.remove("dark");
    themeBtn.textContent = "Switch to Dark Theme";
  }
}

themeBtn.addEventListener("click", () => {
  let newTheme = "dark";

  if (document.body.classList.contains("dark")) {
    newTheme = "light";
  }

  applyTheme(newTheme);

  try {
    localStorage.setItem(themeKey, newTheme);
  } catch (error) {
    console.warn(`The theme could not be saved: ${error.message}`);
  }
});
```

Then load the saved theme inside the existing `try` block, so the page opens in the right theme:

```js
try {
  applyTheme(localStorage.getItem(themeKey));
  countVisit();
  showWelcome();
  loadNotes();
} catch (error) {
  console.error(`Storage is not available: ${error.message}`);
  visitEl.textContent = "Saving is turned off in this browser, so the planner cannot remember you.";
}
```

When nothing has been saved yet, `getItem()` returns `null`, which is not `"dark"`, so `applyTheme()` falls through to the light theme. Test by switching to dark, reloading, and confirming the page stays dark. Then check the Application panel to see `trailhead-theme` saved as the string `dark`.

## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition, Mike Murach & Associates Inc., 2020. Chapter on browser objects and web storage.
- W3Schools, JavaScript Window: https://www.w3schools.com/js/js_window.asp
- W3Schools, JavaScript Window Location: https://www.w3schools.com/js/js_window_location.asp
- W3Schools, JavaScript Window History: https://www.w3schools.com/js/js_window_history.asp
- W3Schools, JavaScript Window Navigator: https://www.w3schools.com/js/js_window_navigator.asp
- W3Schools, JavaScript Window Screen: https://www.w3schools.com/js/js_window_screen.asp
- W3Schools, Web Storage API: https://www.w3schools.com/js/js_api_web_storage.asp
- MDN Web Docs, URLSearchParams: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
