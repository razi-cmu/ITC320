# Week 11 - jQuery Introduction

## Why jQuery

Since Week 4 you have been scripting the DOM with plain JavaScript: `querySelector()`, `textContent`, `classList`, `addEventListener()`, and `createElement()`. jQuery is a JavaScript library that wraps those same tasks in shorter method names. It does not replace JavaScript. A jQuery program is still a JavaScript program, with the same variables, functions, arrays, Maps, and `async`/`await` you already know.

Compare the same task written both ways. Both lines add the class `highlight` to every element with the class `note`:

```javascript
// Plain JavaScript (Week 4)
document.querySelectorAll(".note").forEach((note) => note.classList.add("highlight"));

// jQuery
$(".note").addClass("highlight");
```

Why learn it in this course:

- Many existing websites, content management systems, and plugins still use jQuery, so you will read and maintain jQuery code on the job.
- It is short to write: one method call often works on every matched element, with no loop.
- It has its own AJAX methods.

## Adding jQuery to a Page

jQuery is a single JavaScript file. The simplest way to add it is to load it from the official jQuery CDN (content delivery network) with a `<script>` tag. Put it before your own script, because your script uses `$` and `$` only exists after jQuery has loaded:

```html
  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script src="demo.js"></script>
</body>
```

A few things to know about the file name:

- `4.0.0` is the version. jQuery 4.0 is the current major version. The textbook and many websites use jQuery 3, which works almost the same way for everything in this course.
- `.min` means minified: spaces and comments removed so the file downloads faster. Use it on real pages.
- Do not use the slim build (`jquery-4.0.0.slim.min.js`). It leaves out the AJAX methods you need this week and the effects you need in Week 12.

Because the file loads from the internet, a page that uses the CDN needs an internet connection. It also works on GitHub Pages with no extra setup.

### Checking that jQuery Loaded

Open the page, then type this in the Console:

```javascript
$.fn.jquery
```

The Console shows `"4.0.0"`. If it shows `Uncaught ReferenceError: $ is not defined`, jQuery did not load. Check the spelling of the URL, check that the jQuery `<script>` tag comes before yours, and check your internet connection.

## Demo Files for This Week

Every theory example this week runs on the same small demo page. Create a folder named `jquery-demo` with these three files and open `index.html` with Live Server.

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>jQuery Demo</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1 id="title">jQuery Demo</h1>

  <p class="note">First note</p>
  <p class="note">Second note</p>

  <input id="name-input" type="text" value="Maya">
  <button id="greet-button" type="button">Greet</button>
  <p id="message"></p>

  <ul id="list">
    <li>Apples</li>
    <li>Bread</li>
  </ul>

  <a id="docs-link" href="https://api.jquery.com/">jQuery API</a>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script src="demo.js"></script>
</body>
</html>
```

```css
/* styles.css */
body {
  font-family: Arial, sans-serif;
  margin: 20px;
}

.highlight {
  background: #fff3c4;
}

.done {
  text-decoration: line-through;
  color: #888;
}
```

```javascript
// demo.js
$(function () {
  console.log("jQuery is ready");
});
```

Type each example in this section inside the `$(function () { ... });` block in `demo.js`, save, and reload. Short one-line examples can also be typed straight into the Console.

## The $ Function and the Ready Event

`$` is the name of the jQuery function. (`jQuery` is the long name for the same function, so `jQuery(".note")` and `$(".note")` are identical.) What `$` does depends on what you pass it:

| You pass | `$` does | Example |
| --- | --- | --- |
| A CSS selector string | Selects matching elements | `$(".note")` |
| An HTML string | Creates a new element | `$("<li>")` |
| A function | Runs the function when the DOM is ready | `$(function () { ... })` |

### Running Code When the DOM Is Ready

Code that selects elements must run after those elements exist. Since Week 4 you have handled this by putting your `<script>` tag at the end of `<body>`. jQuery adds a safety net: a function passed to `$` runs as soon as the whole DOM has been built, even if the script tag is in `<head>`.

```javascript
$(function () {
  // Code here runs when the DOM is ready
  console.log($("#title").text());
});
```

You will also see the longer, older form in the textbook and online:

```javascript
$(document).ready(function () {
  console.log($("#title").text());
});
```

Both do the same thing. This course uses the short form and keeps the script tags at the end of `<body>` as before.

## Selecting Elements

jQuery uses the same CSS selectors  used with `querySelector()`:

```javascript
$("#title");          // by id
$(".note");           // by class
$("li");              // by tag name
$("#list li");        // li elements inside #list
```

### A jQuery Object Is a Collection

`$()` always returns a jQuery object: a collection of zero, one, or many matched elements. Methods you call on it act on every element in the collection, so you do not need a loop. This is called implicit iteration.

```javascript
$(".note").addClass("highlight"); // both notes are highlighted
```

Checkpoint: Run the line above. Both paragraphs get a yellow background.

Use `.length` to see how many elements were matched:

```javascript
console.log($(".note").length);   // 2
console.log($("#nothing").length); // 0
```

### No Match Means No Error

`document.querySelector("#nothing").textContent = "Hi";` crashes with a `TypeError`, because `querySelector()` returns `null`. jQuery behaves differently: an empty selection is still a jQuery object, so methods called on it quietly do nothing.

```javascript
$("#nothing").text("Hi"); // no error, nothing happens
```

This is convenient, but it also hides typos. If a jQuery line seems to do nothing, check the selector's `.length` in the Console first.

| Task | Plain JavaScript (Week 4) | jQuery |
| --- | --- | --- |
| One element by id | `document.querySelector("#title")` | `$("#title")` |
| All matching elements | `document.querySelectorAll(".note")` | `$(".note")` |
| Nothing matched | `null` (or an empty NodeList) | An empty jQuery object, `.length` is `0` |
| Change every match | Loop with `forEach()` | Call the method once |

## Getting and Setting Content

Most jQuery methods are both getters and setters. Called with no argument, the method reads a value. Called with an argument, it writes the value.

| Method | Plain JavaScript equivalent | Use it for |
| --- | --- | --- |
| `.text()` | `textContent` | Plain text |
| `.html()` | `innerHTML` | HTML markup |
| `.val()` | `value` | Form fields (`input`, `select`, `textarea`) |

```javascript
console.log($("#title").text());   // "jQuery Demo"
$("#title").text("Hello, jQuery");

console.log($("#name-input").val()); // "Maya"
$("#name-input").val("Sam");
```

When a selection holds more than one element, the getters behave differently: `.text()` joins the text of all matched elements, while `.html()` and `.val()` read only the first one.

```javascript
console.log($(".note").text()); // "First noteSecond note"
console.log($(".note").html()); // "First note"
```

The Week 4 advice still applies: `.html()` turns a string into real HTML, so never pass it text that came from a user or a web API. Use `.text()` for that. Week 14 explains why in detail.

## Attributes, Classes, and Styles

### Attributes

`.attr()` reads or writes any HTML attribute, like `getAttribute()` and `setAttribute()` from Week 4:

```javascript
console.log($("#docs-link").attr("href")); // "https://api.jquery.com/"
$("#docs-link").attr("target", "_blank");  // open in a new tab
```

### Classes

These match `classList.add()`, `remove()`, `toggle()`, and `contains()` from Weeks 4 and 9:

```javascript
$("#list li").addClass("done");
$("#list li").removeClass("done");
$("#list li").toggleClass("done");
console.log($("#list li").hasClass("done")); // true or false
```

### Styles

`.css()` reads or sets an inline style:

```javascript
$("#title").css("color", "#3d5a80");
console.log($("#title").css("color")); // "rgb(61, 90, 128)"
```

## Chaining

Most jQuery methods that change something return the same jQuery object, so you can call another method on the result. This is called chaining:

```javascript
$("#title").text("Chained").addClass("highlight").css("color", "#ee6c4d");
```

Long chains are easier to read with one method per line:

```javascript
$("#title")
  .text("Chained")
  .addClass("highlight")
  .css("color", "#ee6c4d");
```

A getter ends a chain, because it returns a value (a string), not a jQuery object. `$("#title").text().addClass("x")` fails for this reason.

## Handling Events

`.on()` attaches an event listener, like `addEventListener()`. It uses the same event names: `click`, `mouseenter`, `keydown`, `input`, `submit`, and so on.

```javascript
$("#greet-button").on("click", function () {
  const name = $("#name-input").val();
  $("#message").text(`Hello, ${name}!`);
});
```

Checkpoint: Click Greet. The page shows "Hello, Maya!". Change the name and click again.

Because of implicit iteration, one `.on()` call attaches the listener to every matched element:

```javascript
$("#list li").on("click", function () {
  console.log("You clicked a list item");
});
```

### The Event Object and $(this)

The handler receives the same event object, so `event.type` and `event.target` still work. Inside a handler written with `function`, the keyword `this` is the element that the listener is attached to. Wrap it in `$()` to use jQuery methods on it:

```javascript
$("#list li").on("click", function (event) {
  console.log(event.type);       // "click"
  $(this).toggleClass("done");    // only the clicked item changes
});
```

Checkpoint: Click Apples. Only Apples is crossed out. Click it again to undo.

Use a regular `function`, not an arrow function, when you need `this`. Arrow functions do not get their own `this`, so `$(this)` inside an arrow function does not point to the clicked element. Arrow functions are still fine for handlers that do not use `this`.

## Creating and Adding Elements

Pass an HTML tag to `$` to create a new element, like `createElement()`. Then set its content and add it to the page:

```javascript
const item = $("<li>").text("Milk");
$("#list").append(item);
```

| Method | What it does |
| --- | --- |
| `a.append(b)` | Adds `b` as the last child of `a` |
| `a.prepend(b)` | Adds `b` as the first child of `a` |
| `b.appendTo(a)` | Same as `a.append(b)`, but written from the new element's side, so it fits at the end of a chain |
| `.remove()` | Removes the element from the page (like Week 4's `remove()`) |
| `.empty()` | Removes all of the element's children, keeping the element |

`appendTo()` lets you build and add an element in one chain:

```javascript
$("<li>").text("Eggs").addClass("highlight").appendTo("#list");
```

`append()` accepts several elements at once, which is useful for building a small card:

```javascript
const card = $("<div>").append(
  $("<h3>").text("Paper Moons"),
  $("<span>").text("Omar Reyes")
);

$("body").append(card);
```

`.empty()` is the jQuery version of the `innerHTML = ""` line to clear a list before redrawing it:

```javascript
$("#list").empty(); // the ul stays, its li items are gone
```

## Looping with .each()

Implicit iteration covers most cases, but sometimes each element needs its own work. `.each()` runs a function once per matched element. The function receives the index, and `this` is the current element:

```javascript
$("#list li").each(function (index) {
  $(this).text(`${index + 1}. ${$(this).text()}`);
});
```

Checkpoint: The list items now read "1. Apples", "2. Bread", and so on.

`.each()` is for jQuery collections. For arrays, Sets, and Maps, keep using `for...of` and `forEach()` from Week 10. The exercises this week mix both.

## Exercise 1: Reading List in jQuery

The Northside Book Club wants to keep using the Reading List page from Week 10, and a new club volunteer only knows jQuery. Rewrite the page's script in jQuery. The page should look and behave exactly as it did in Week 10: every book shown as a card, genre buttons built from a Set, filtering by genre, and votes counted in a Map.

Make a new folder for this exercise. The book titles and authors are fictional.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Northside Book Club</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Northside Book Club</h1>
    <p>This season's reading list</p>
  </header>

  <main>
    <section id="controls">
      <div id="genre-buttons"></div>
    </section>

    <ul id="book-list"></ul>

    <section id="votes">
      <h2>Votes</h2>
      <ul id="vote-list"></ul>
    </section>
  </main>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script src="books.js"></script>
</body>
</html>
```

Given: `styles.css`

```css
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background: #f6f4ef;
  color: #2b2b2b;
}

header {
  background: #3d5a80;
  color: white;
  padding: 20px;
  text-align: center;
}

header h1 {
  margin: 0;
}

main {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

#controls,
#genre-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 16px 0;
}

button {
  background: white;
  border: 1px solid #3d5a80;
  color: #3d5a80;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background: #e0e7f1;
}

button.active {
  background: #3d5a80;
  color: white;
}

#book-list,
#vote-list {
  list-style: none;
  padding: 0;
}

.book {
  background: white;
  border-left: 4px solid #ee6c4d;
  padding: 12px 16px;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
}

.book-info h3 {
  margin: 0 0 4px;
}

.book-info span {
  font-size: 0.9em;
  color: #666;
}

#vote-list {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

#vote-list li {
  background: #3d5a80;
  color: white;
  padding: 4px 12px;
  border-radius: 16px;
}
```

### Step 1: Load jQuery and Store the Books

Create `books.js`. Put all of your code inside a ready function, and start with the same `books` array as Week 10:

```javascript
// books.js

$(function () {
  const books = [
    { title: "The Silent Orchard", author: "Lena Hart", genre: "Mystery", pages: 312 },
    { title: "Paper Moons", author: "Omar Reyes", genre: "Fantasy", pages: 458 },
    { title: "Field Notes", author: "Priya Nand", genre: "Nonfiction", pages: 224 },
    { title: "The Last Ferry", author: "Sam Okafor", genre: "Mystery", pages: 276 },
    { title: "Glass Harbor", author: "Ines Duarte", genre: "Fantasy", pages: 390 },
    { title: "Small Habits", author: "Theo Lund", genre: "Nonfiction", pages: 198 }
  ];

  console.log($.fn.jquery, books.length);
});
```

The array is plain JavaScript. jQuery changes how you work with the page, not how you store data.

Checkpoint: Open the page with Live Server. The Console shows `4.0.0 6`.

### Step 2: Display the Books

Write `renderBooks()` inside the ready function, below the array. Each book becomes an `li` card with an `h3` title and a `span` of details, the same structure as Week 10, built with `$("<tag>")`, `.text()`, and `.append()`. Replace the `console.log()` line with a call to `renderBooks(books);`.

```javascript
  function renderBooks(list) {
    $("#book-list").empty();

    list.forEach((book) => {
      const info = $("<div>").addClass("book-info").append(
        $("<h3>").text(book.title),
        $("<span>").text(`${book.author} | ${book.genre} | ${book.pages} pages`)
      );

      $("<li>").addClass("book").append(info).appendTo("#book-list");
    });
  }

  renderBooks(books);
```

Notice the mix: `forEach()` loops through the array, and jQuery builds and adds the elements.

Checkpoint: All six books appear as cards with their title, author, genre, and page count.

### Step 3: Build Genre Buttons from a Set

Write `addGenreButton()`, which creates one button and adds it to `#genre-buttons`. Then build the Set of genres, add an "All" button, and add one button per genre. Put these calls just above `renderBooks(books);`.

```javascript
  function addGenreButton(label) {
    $("<button>")
      .attr("type", "button")
      .text(label)
      .appendTo("#genre-buttons");
  }

  const genres = new Set(books.map((book) => book.genre));

  addGenreButton("All");
  genres.forEach((genre) => addGenreButton(genre));
```

Checkpoint: Four buttons appear: All, Mystery, Fantasy, Nonfiction. Clicking them does nothing yet.

### Step 4: Filter by Genre

Chain an `.on("click", ...)` call into `addGenreButton()`, between `.text(label)` and `.appendTo(...)`. The handler uses `label`, so it knows which button was clicked without needing `this`.

```javascript
  function addGenreButton(label) {
    $("<button>")
      .attr("type", "button")
      .text(label)
      .on("click", () => {
        if (label === "All") {
          renderBooks(books);
        } else {
          renderBooks(books.filter((book) => book.genre === label));
        }
      })
      .appendTo("#genre-buttons");
  }
```

Checkpoint: Clicking Mystery shows only The Silent Orchard and The Last Ferry. Clicking All brings back all six.

### Step 5: Count Votes with a Map

Add the `votes` Map and an `addVote()` function using the counting pattern from Week 10. Put the Map just below the `books` array.

```javascript
  const votes = new Map();

  function addVote(title) {
    if (votes.has(title)) {
      votes.set(title, votes.get(title) + 1);
    } else {
      votes.set(title, 1);
    }

    console.log(votes);
  }
```

In `renderBooks()`, create a Vote button for each book and add it to the card next to `info`:

```javascript
      const voteButton = $("<button>")
        .attr("type", "button")
        .text("Vote")
        .on("click", () => addVote(book.title));

      $("<li>").addClass("book").append(info, voteButton).appendTo("#book-list");
```

Checkpoint: Click Vote on Paper Moons twice. The Console shows the Map with Paper Moons mapped to 2. Click Fantasy, vote for Paper Moons again, and the count becomes 3.

### Step 6: Show the Votes

Write `showVotes()`, and call it from `addVote()` in place of `console.log(votes);`.

```javascript
  function showVotes() {
    $("#vote-list").empty();

    votes.forEach((count, title) => {
      $("<li>").text(`${title}: ${count}`).appendTo("#vote-list");
    });
  }
```

Checkpoint: Vote for Paper Moons twice and Field Notes once. The Votes section shows two pills: "Paper Moons: 2" and "Field Notes: 1".

### Solution

```javascript
// books.js

$(function () {
  const books = [
    { title: "The Silent Orchard", author: "Lena Hart", genre: "Mystery", pages: 312 },
    { title: "Paper Moons", author: "Omar Reyes", genre: "Fantasy", pages: 458 },
    { title: "Field Notes", author: "Priya Nand", genre: "Nonfiction", pages: 224 },
    { title: "The Last Ferry", author: "Sam Okafor", genre: "Mystery", pages: 276 },
    { title: "Glass Harbor", author: "Ines Duarte", genre: "Fantasy", pages: 390 },
    { title: "Small Habits", author: "Theo Lund", genre: "Nonfiction", pages: 198 }
  ];

  const votes = new Map();

  function renderBooks(list) {
    $("#book-list").empty();

    list.forEach((book) => {
      const info = $("<div>").addClass("book-info").append(
        $("<h3>").text(book.title),
        $("<span>").text(`${book.author} | ${book.genre} | ${book.pages} pages`)
      );

      const voteButton = $("<button>")
        .attr("type", "button")
        .text("Vote")
        .on("click", () => addVote(book.title));

      $("<li>").addClass("book").append(info, voteButton).appendTo("#book-list");
    });
  }

  function addGenreButton(label) {
    $("<button>")
      .attr("type", "button")
      .text(label)
      .on("click", () => {
        if (label === "All") {
          renderBooks(books);
        } else {
          renderBooks(books.filter((book) => book.genre === label));
        }
      })
      .appendTo("#genre-buttons");
  }

  function addVote(title) {
    if (votes.has(title)) {
      votes.set(title, votes.get(title) + 1);
    } else {
      votes.set(title, 1);
    }

    showVotes();
  }

  function showVotes() {
    $("#vote-list").empty();

    votes.forEach((count, title) => {
      $("<li>").text(`${title}: ${count}`).appendTo("#vote-list");
    });
  }

  const genres = new Set(books.map((book) => book.genre));

  addGenreButton("All");
  genres.forEach((genre) => addGenreButton(genre));

  renderBooks(books);
});
```

Compare this file with your Week 10 `books.js`. The data, the Set, the Map, and the logic are the same. Only the DOM code changed, and it is shorter.

**Challenge:** Highlight the genre button that is currently selected, using the `button.active` rule already in `styles.css`. When a genre button is clicked, remove `active` from every genre button, then add it to the clicked one. The All button should start out highlighted.

The handler now needs the clicked button, so change it from an arrow function to a regular `function` and use `$(this)`:

```javascript
      .on("click", function () {
        $("#genre-buttons button").removeClass("active");
        $(this).addClass("active");

        if (label === "All") {
          renderBooks(books);
        } else {
          renderBooks(books.filter((book) => book.genre === label));
        }
      })
```

Then highlight the All button when the page loads, just after the genre buttons are created. `.first()` keeps only the first element of a selection:

```javascript
  addGenreButton("All");
  genres.forEach((genre) => addGenreButton(genre));
  $("#genre-buttons button").first().addClass("active");
```

Checkpoint: The All button starts dark blue. Clicking Fantasy makes Fantasy dark blue and All white again.

## Traversing the DOM

In Week 4 you moved around the DOM tree with `parentElement`, `children`, and `closest()`. jQuery has matching methods, and each one returns a jQuery object, so you can keep chaining.

| Method | Returns | Week 4 equivalent |
| --- | --- | --- |
| `.parent()` | The direct parent | `parentElement` |
| `.children()` | The direct children (optionally filtered by a selector) | `children` |
| `.find(selector)` | Matching descendants at any depth | `querySelectorAll()` on an element |
| `.closest(selector)` | The nearest ancestor (or the element itself) that matches | `closest()` |
| `.siblings()` | The other children of the same parent | (no direct equivalent) |

Add this list to the demo `index.html`, just above the jQuery `<script>` tag. It is used for the rest of this week's theory:

```html
  <ul id="shelf">
    <li class="shelf-book"><span>Paper Moons</span> <button class="like" type="button">Like</button></li>
    <li class="shelf-book"><span>Field Notes</span> <button class="like" type="button">Like</button></li>
  </ul>
  <button id="add-book" type="button">Add Book</button>
```

Try these in the Console:

```javascript
$("#shelf").children().length;          // 2
$("#shelf").find("span").first().text(); // "Paper Moons"
$(".like").first().closest("li").text(); // "Paper Moons Like"
```

A common use for `.closest()` is inside a click handler: from the clicked button, find the card it belongs to.

```javascript
$(".like").on("click", function () {
  $(this).closest("li").addClass("highlight");
});
```

Checkpoint: Click Like next to Field Notes. Only the Field Notes row turns yellow.

## Event Delegation

Add this handler to `demo.js`. It adds a new book, with its own Like button, to the shelf:

```javascript
$("#add-book").on("click", function () {
  const count = $("#shelf li").length + 1;

  $("<li>").addClass("shelf-book").append(
    $("<span>").text(`New Book ${count}`),
    " ",
    $("<button>").addClass("like").attr("type", "button").text("Like")
  ).appendTo("#shelf");
});
```

Click Add Book, then click Like on the new book. Nothing happens. The `$(".like").on("click", ...)` line from the previous section ran once, when the page loaded, and attached listeners only to the two buttons that existed at that moment. Buttons created later never got one.

Event delegation fixes this. Instead of attaching a listener to every button, attach one listener to a parent that always exists (`#shelf`), and give `.on()` a second argument: a selector saying which children you care about.

```javascript
$("#shelf").on("click", ".like", function () {
  $(this).closest("li").addClass("highlight");
});
```

Replace the earlier `$(".like").on(...)` handler with this version.

Checkpoint: Add two new books. Clicking Like on any book, old or new, highlights that row.

How it works: a click on a button also counts as a click on every element that contains it (the click "bubbles up" through its ancestors). The listener on `#shelf` hears the click, checks whether it started inside an element matching `.like`, and if so runs the handler with `this` set to that button.

Use delegation when:

- Elements are added after the page loads (like the Add Book rows).
- A list is cleared and redrawn, as `renderBooks()` does every time you filter or search.
- There are many similar elements. One listener on the parent is simpler than one per child.

## Storing Data on Elements with .data()

A delegated handler often needs to know which item was clicked. `.data()` stores a value on an element and reads it back later:

```javascript
const button = $("<button>").text("Vote").data("title", "Paper Moons");

console.log(button.data("title")); // "Paper Moons"
```

When `.data()` reads a `data-*` attribute from the HTML, it converts values that look like numbers or booleans. `data-title="1984"` comes back as the number `1984`, and `data-flag="true"` as the boolean `true`. Values you store with `.data(key, value)` come back exactly as stored, so the exercise stores titles that way. If you ever need the raw string from the HTML, use `.attr("data-title")`.

## Working with Forms

You can already read a field with `.val()`. Two more pieces make a working search form.

Add this form to the demo `index.html`, just above the jQuery `<script>` tag:

```html
  <form id="search-form">
    <input id="search-box" type="search" placeholder="Search by title">
    <button type="submit">Search</button>
  </form>
  <p id="output"></p>
```

Listen for the form's `submit` event, not the button's `click`. `submit` fires both when the button is clicked and when the user presses Enter in the field.

```javascript
$("#search-form").on("submit", function (event) {
  event.preventDefault();

  const query = $("#search-box").val().trim();

  if (query === "") {
    $("#output").text("Please type something.");
    return;
  }

  $("#output").text(`You searched for: ${query}`);
});
```

- `event.preventDefault()` stops the browser's default action for the event. For a form, the default is to submit it and reload the page, which would wipe out everything your script did. Remove the line and try a search to see the page reload.
- `.trim()` is the plain JavaScript string method from Week 8. Older jQuery code uses `$.trim(value)`, which was removed in jQuery 4, so use the string method.
- The `if` block is a guard clause (Week 5) that stops on an empty search.

Checkpoint: Type "harbor" and press Enter. The page shows "You searched for: harbor" and does not reload. Submit an empty box and the page asks you to type something.

## AJAX with jQuery

In Week 10 you requested data with `fetch()`, `await`, and `response.json()`. jQuery has its own AJAX methods. The one you will use most is `$.getJSON()`, which requests a URL and parses the JSON response in a single step.

### Demo File

Add a file named `books.json` to the `jquery-demo` folder. It holds the same six books as the Week 10 demo:

```json
[
  { "title": "The Silent Orchard", "author": "Lena Hart", "genre": "Mystery", "pages": 312 },
  { "title": "Paper Moons", "author": "Omar Reyes", "genre": "Fantasy", "pages": 458 },
  { "title": "Field Notes", "author": "Priya Nand", "genre": "Nonfiction", "pages": 224 },
  { "title": "The Last Ferry", "author": "Sam Okafor", "genre": "Mystery", "pages": 276 },
  { "title": "Glass Harbor", "author": "Ines Duarte", "genre": "Fantasy", "pages": 390 },
  { "title": "Small Habits", "author": "Theo Lund", "genre": "Nonfiction", "pages": 198 }
]
```

As in Week 10, the page must be opened with Live Server (an `http://` address). A page opened from `file:///` cannot request files, and the Console shows a CORS error.

### $.getJSON() with await

jQuery's AJAX methods return an object that works with `await`, so you can use the same `async`/`await` style as Week 10:

```javascript
async function showBooks() {
  const books = await $.getJSON("books.json");
  console.log(books.length);   // 6
  console.log(books[0].title); // "The Silent Orchard"
}

showBooks();
```

Compare it with the Week 10 version:

```javascript
// Week 10: fetch()
const response = await fetch("books.json");
const books = await response.json();

// Week 11: jQuery
const books = await $.getJSON("books.json");
```

One `await` instead of two: `$.getJSON()` reads and parses the response for you.

### Handling Errors

Wrap the request in `try...catch`, as in Week 10. There is one important difference from `fetch()`:

| Situation | `fetch()` (Week 10) | `$.getJSON()` |
| --- | --- | --- |
| Network failure | Throws an error | Throws an error |
| 404 or other HTTP error | Does not throw; you check `response.ok` and `throw` yourself | Throws an error |
| Invalid JSON | `response.json()` throws a `SyntaxError` | Throws an error |

So with jQuery there is no `response.ok` check. Any failure lands in `catch`. The caught value is jQuery's request object, and its `status` property holds the HTTP status code (`0` for a network failure):

```javascript
async function showBooks() {
  try {
    const books = await $.getJSON("book.json"); // misspelled on purpose
    console.log(books.length);
  } catch (error) {
    console.error(`Request failed with status ${error.status}`);
  }
}

showBooks();
```

Checkpoint: The Console shows "Request failed with status 404". Fix the file name to `books.json`, reload, and it shows 6.

### Sending Query Parameters

In Week 10 you built query strings with `URLSearchParams`. With jQuery, pass an object as the second argument and jQuery builds the query string for you:

```javascript
async function searchTitles(query) {
  try {
    const data = await $.getJSON("https://openlibrary.org/search.json", {
      title: query,
      fields: "title,author_name",
      limit: 5
    });

    data.docs.forEach((doc) => console.log(doc.title));
  } catch (error) {
    console.error(`Search failed with status ${error.status}`);
  }
}

searchTitles("harbor");
```

Open the Network panel in DevTools and click the request. The address jQuery built is the same one you built by hand in Week 10: `https://openlibrary.org/search.json?title=harbor&fields=title%2Cauthor_name&limit=5`.

Checkpoint: The Console lists up to five book titles that contain "harbor". The exact titles depend on Open Library's data on the day you run it.

### $.ajax(): The General Form

`$.getJSON()` is a shortcut for jQuery's general AJAX method, `$.ajax()`, which takes a single object of options. These two lines request the same data:

```javascript
const books1 = await $.getJSON("books.json");
const books2 = await $.ajax({ url: "books.json", dataType: "json" });
```

`$.ajax()` can also send data to a server (for example `method: "POST"`), set headers, and much more. In this course you only read data, so `$.getJSON()` is enough.

### The Older Callback Style

Code written before `async`/`await` existed, including much of what you find online, uses `.done()` and `.fail()` instead of `await` and `try...catch`:

```javascript
$.getJSON("books.json")
  .done(function (books) {
    console.log(books.length);
  })
  .fail(function (error) {
    console.error(`Request failed with status ${error.status}`);
  });
```

`.done()` runs when the request succeeds, and `.fail()` runs when it fails. It does the same job as the `try...catch` version above. Read it when you meet it, but write the `await` version in this course.

## Exercise 2: Reading List with Open Library, in jQuery

Rewrite Week 10's Open Library version of the Reading List in jQuery, and fix a hidden problem: in Exercise 1, every Vote button got its own listener each time `renderBooks()` ran. With search results that change on every search, one delegated listener is cleaner. You will also use a real search form, so pressing Enter works.

Make a copy of your Exercise 1 folder and work in the copy. Open it with Live Server.

In `index.html`, replace the `<div id="genre-buttons"></div>` line with a search form, and add a status paragraph just below the `#controls` section:

```html
    <section id="controls">
      <form id="search-form">
        <input id="search-input" type="search" placeholder="Search by title">
        <button type="submit">Search</button>
      </form>
    </section>

    <p id="status"></p>
```

Add these rules to the end of `styles.css`:

```css
#search-form {
  display: flex;
  gap: 8px;
  width: 100%;
}

#search-input {
  flex: 1;
  padding: 7px 10px;
  border: 1px solid #3d5a80;
  border-radius: 4px;
}

#status {
  color: #666;
}
```

### Step 1: Remove the Hard-Coded Books

In `books.js`, delete the `books` array, the `addGenreButton()` function, the `genres` Set, and the calls that build the genre buttons and run `renderBooks(books)`. Keep `votes`, `renderBooks()`, `addVote()`, and `showVotes()`.

Change `const votes` to `let votes`, because Step 6 replaces the Map with one loaded from storage. Add a storage key constant below it, as in Week 10:

```javascript
  const VOTES_KEY = "northside-votes";
  let votes = new Map();
```

Open Library gives only a title and authors, so change the details line in `renderBooks()` to show just the author:

```javascript
        $("<span>").text(book.author)
```

Checkpoint: Reload. The page shows the header, the search box, and an empty Votes section, with no errors in the Console.

### Step 2: Fetch Books with $.getJSON()

Add an `async` function that searches Open Library by title. It shows a message in `#status` while it works and if it fails. At the bottom of the ready function, call it with a starting search.

```javascript
  async function loadBooks(query) {
    $("#status").text("Searching...");

    try {
      const data = await $.getJSON("https://openlibrary.org/search.json", {
        title: query,
        fields: "title,author_name",
        limit: 6
      });

      console.log(data.docs);
    } catch (error) {
      $("#status").text(`Search failed (status ${error.status}). Try again.`);
      console.error(error);
    }
  }

  loadBooks("harbor");
```

Checkpoint: The Console shows an array of up to six objects, each with a `title` and usually an `author_name` array. The status still says "Searching...", which you fix next.

### Step 3: Convert and Display the Results

`renderBooks()` expects objects with `title` and `author`. Use `map()` to convert each result, with the Week 10 fallback for a missing `author_name`, then render them and update the status. Replace the `console.log(data.docs);` line with:

```javascript
      const books = data.docs.map((doc) => ({
        title: doc.title,
        author: doc.author_name ? doc.author_name.join(", ") : "Unknown author"
      }));

      renderBooks(books);
      $("#status").text(`Found ${books.length} books for "${query}".`);
```

Checkpoint: Up to six book cards appear, and the status shows how many were found. Books with no author show "Unknown author".

### Step 4: Search with the Form

Add a `submit` handler for the form near the bottom of the ready function, above `loadBooks("harbor");`:

```javascript
  $("#search-form").on("submit", function (event) {
    event.preventDefault();

    const query = $("#search-input").val().trim();

    if (query === "") {
      $("#status").text("Type a title to search.");
      return;
    }

    loadBooks(query);
  });
```

Checkpoint: Type "garden" and press Enter. The list changes to garden titles without the page reloading. Searching with an empty box shows "Type a title to search."

### Step 5: Vote with Event Delegation

In `renderBooks()`, remove the `.on("click", ...)` line from the Vote button. Instead, give the button a class and store the book's title on it with `.data()`:

```javascript
      const voteButton = $("<button>")
        .attr("type", "button")
        .addClass("vote-btn")
        .text("Vote")
        .data("title", book.title);
```

Then add one delegated listener on `#book-list`, which always exists, next to the form handler:

```javascript
  $("#book-list").on("click", ".vote-btn", function () {
    addVote($(this).data("title"));
  });
```

This single listener handles every Vote button, including buttons created by searches that have not happened yet.

Checkpoint: Vote for two books, search for something else, and vote for a book in the new results. Every Vote button works, and the Votes section keeps all the counts.

### Step 6: Save and Load the Votes

Reloading the page still loses the votes. Add the same save and load code you wrote in Week 10. Nothing in it is jQuery: plain JavaScript and jQuery work side by side in one file.

```javascript
  function saveVotes() {
    localStorage.setItem(VOTES_KEY, JSON.stringify(Array.from(votes)));
  }

  function loadVotes() {
    const saved = localStorage.getItem(VOTES_KEY);

    if (saved === null) {
      return;
    }

    try {
      const parsed = JSON.parse(saved);

      if (Array.isArray(parsed)) {
        votes = new Map(parsed);
      }
    } catch (error) {
      console.warn("Saved votes were not valid JSON.");
    }
  }
```

Call `saveVotes();` in `addVote()`, just before `showVotes();`. At the bottom of the ready function, load and show the saved votes before the first search:

```javascript
  loadVotes();
  showVotes();
  loadBooks("harbor");
```

Checkpoint: Vote for a few books and reload. The Votes section comes back with the same counts. In the Application panel, `northside-votes` holds the votes as a JSON array of pairs.

### Solution

```javascript
// books.js

$(function () {
  const VOTES_KEY = "northside-votes";
  let votes = new Map();

  function renderBooks(list) {
    $("#book-list").empty();

    list.forEach((book) => {
      const info = $("<div>").addClass("book-info").append(
        $("<h3>").text(book.title),
        $("<span>").text(book.author)
      );

      const voteButton = $("<button>")
        .attr("type", "button")
        .addClass("vote-btn")
        .text("Vote")
        .data("title", book.title);

      $("<li>").addClass("book").append(info, voteButton).appendTo("#book-list");
    });
  }

  async function loadBooks(query) {
    $("#status").text("Searching...");

    try {
      const data = await $.getJSON("https://openlibrary.org/search.json", {
        title: query,
        fields: "title,author_name",
        limit: 6
      });

      const books = data.docs.map((doc) => ({
        title: doc.title,
        author: doc.author_name ? doc.author_name.join(", ") : "Unknown author"
      }));

      renderBooks(books);
      $("#status").text(`Found ${books.length} books for "${query}".`);
    } catch (error) {
      $("#status").text(`Search failed (status ${error.status}). Try again.`);
      console.error(error);
    }
  }

  function addVote(title) {
    if (votes.has(title)) {
      votes.set(title, votes.get(title) + 1);
    } else {
      votes.set(title, 1);
    }

    saveVotes();
    showVotes();
  }

  function showVotes() {
    $("#vote-list").empty();

    votes.forEach((count, title) => {
      $("<li>").text(`${title}: ${count}`).appendTo("#vote-list");
    });
  }

  function saveVotes() {
    localStorage.setItem(VOTES_KEY, JSON.stringify(Array.from(votes)));
  }

  function loadVotes() {
    const saved = localStorage.getItem(VOTES_KEY);

    if (saved === null) {
      return;
    }

    try {
      const parsed = JSON.parse(saved);

      if (Array.isArray(parsed)) {
        votes = new Map(parsed);
      }
    } catch (error) {
      console.warn("Saved votes were not valid JSON.");
    }
  }

  $("#book-list").on("click", ".vote-btn", function () {
    addVote($(this).data("title"));
  });

  $("#search-form").on("submit", function (event) {
    event.preventDefault();

    const query = $("#search-input").val().trim();

    if (query === "") {
      $("#status").text("Type a title to search.");
      return;
    }

    loadBooks(query);
  });

  loadVotes();
  showVotes();
  loadBooks("harbor");
});
```

To test the error message, change `search.json` in the URL to `search.jsn` and reload. The status shows "Search failed" with an error status such as 404. To see status 0, turn on Offline in the Network panel (Week 9) and search again. Change the URL back afterward.

**Challenge:** Show each book's vote count on its button, for example "Vote (2)", and mark voted books by adding a `voted` class to their card. The count and the mark should also appear when a voted book shows up again in a later search.

Add this rule to `styles.css`:

```css
.book.voted {
  border-left-color: #3d5a80;
}
```

In the delegated handler, update the clicked button and use `.closest()` to find its card:

```javascript
  $("#book-list").on("click", ".vote-btn", function () {
    const title = $(this).data("title");

    addVote(title);
    $(this).text(`Vote (${votes.get(title)})`);
    $(this).closest(".book").addClass("voted");
  });
```

In `renderBooks()`, store the card in a variable so you can mark books that already have votes before adding the card to the page:

```javascript
      const card = $("<li>").addClass("book").append(info, voteButton);

      if (votes.has(book.title)) {
        voteButton.text(`Vote (${votes.get(book.title)})`);
        card.addClass("voted");
      }

      card.appendTo("#book-list");
```

Checkpoint: Vote twice for the first book. Its button reads "Vote (2)" and its card's left border turns blue. Search for "harbor" again, or reload the page: the same book still shows "Vote (2)" and the blue border.

## References

- Delamater, M. (2020). Murach's JavaScript and jQuery (4th ed.). Mike Murach & Associates. 
  - Chapter 8: Get off to a fast start with jQuery
- jQuery API Documentation: https://api.jquery.com/
- jQuery Learning Center: https://learn.jquery.com/
- jQuery 4.0 Upgrade Guide: https://jquery.com/upgrade-guide/4.0/
- W3Schools, jQuery Tutorial: https://www.w3schools.com/jquery/
- W3Schools, jQuery Event Methods: https://www.w3schools.com/jquery/jquery_events.asp
