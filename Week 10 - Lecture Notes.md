# Week 10: Arrays, Sets, Maps, JSON, and AJAX

So far, every variable we have written holds one value. Real applications deal with lists: a list of books, a list of search results, a list of items in a cart. This week we learn the three built-in JavaScript collections (arrays, sets, and maps), then learn how to save them in Web Storage with JSON and load them from files and web services with AJAX.

---

## Why We Need Collections

Storing a reading list in separate variables (`book1`, `book2`, `book3`) breaks down quickly. You cannot loop over them, count them, or ask whether a book is on the list. A collection stores many values under one name and gives us methods to add, remove, search, and loop.

| Collection | What it stores | Typical use |
| --- | --- | --- |
| Array | An ordered list of values, accessed by position | A reading list, search results |
| Set | A group of unique values, no duplicates | The distinct genres in a list of books |
| Map | Key and value pairs, accessed by key | Vote counts per book title |

---

## Creating Arrays

An array is written with square brackets. Each value is called an element, and each element has a position called its index. Indexes start at 0, just like the months in `getMonth()` from Week 8.

```javascript
const colors = ["red", "green", "blue"];

console.log(colors[0]);                 // "red"
console.log(colors.length);             // 3
console.log(colors[colors.length - 1]); // "blue", the last element
console.log(colors[5]);                 // undefined, no error
```

Reading an index that does not exist quietly returns `undefined`, the same kind of silent failure we learned to guard against in Week 5.

You can replace an element by assigning to its index. This works even with `const`, because `const` stops the variable from pointing at a different array, not the contents from changing:

```javascript
colors[1] = "purple";
console.log(colors); // ["red", "purple", "blue"]
```

### Arrays of objects

The most useful arrays hold objects:

```javascript
const students = [
  { name: "Maya", major: "Biology", credits: 45 },
  { name: "Leo", major: "History", credits: 30 },
  { name: "Ana", major: "Biology", credits: 60 }
];

console.log(students[1].name); // "Leo"
```

---

## Adding and Removing Elements

| Method | What it does |
| --- | --- |
| `push(value)` | Adds to the end |
| `pop()` | Removes from the end and returns it |
| `unshift(value)` | Adds to the start |
| `shift()` | Removes from the start and returns it |

```javascript
const queue = ["Maya", "Leo"];

queue.push("Ana");        // ["Maya", "Leo", "Ana"]
const first = queue.shift(); // first is "Maya", queue is ["Leo", "Ana"]
```

These methods change the original array.

---

## Looping Through Arrays

There are three common ways to loop through an array:

```javascript
const scores = [88, 92, 75];

// Classic for loop, when you need the index
for (let i = 0; i < scores.length; i++) {
  console.log(scores[i]);
}

// for...of, when you only need the values (as with NodeLists in Week 4)
for (const score of scores) {
  console.log(score);
}

// forEach, which runs a function for each element
scores.forEach((score, index) => {
  console.log(`Score ${index + 1}: ${score}`);
});
```

In the classic loop, use `i < scores.length`, not `<=`. The last index is `length - 1`, and going one past it is the off-by-one error from Week 5.

---

## Searching Arrays

`includes()` and `indexOf()` work the same way as the string methods from Week 8:

```javascript
const colors = ["red", "green", "blue"];

console.log(colors.includes("green")); // true
console.log(colors.indexOf("blue"));   // 2
console.log(colors.indexOf("pink"));   // -1, not found
```

For an array of objects, use `find()`. It takes a function that tests each element and returns the first element that passes, or `undefined` if none does:

```javascript
const leo = students.find((student) => student.name === "Leo");
console.log(leo.credits); // 30

const zoe = students.find((student) => student.name === "Zoe");
console.log(zoe); // undefined, so check before using zoe.credits
```

---

## filter(), map(), and reduce()

A callback is a function you pass to another function, which calls it for you. You have used them already with `addEventListener()` and `setTimeout()`. These three array methods take callbacks, and none of them change the original array.

`filter()` keeps the elements that pass a test:

```javascript
const biologyStudents = students.filter((student) => student.major === "Biology");
console.log(biologyStudents.length); // 2
```

`map()` builds a new array by transforming every element:

```javascript
const names = students.map((student) => student.name);
console.log(names); // ["Maya", "Leo", "Ana"]
```

`reduce()` combines all elements into one value. The callback receives the running result and the current element. The second argument, `0` here, is the starting value; always include it.

```javascript
const totalCredits = students.reduce((sum, student) => sum + student.credits, 0);
console.log(totalCredits); // 135
```

---

## Sorting Arrays

By default, `sort()` compares elements as strings, which gives surprising results for numbers:

```javascript
const numbers = [10, 9, 100, 1];
numbers.sort();
console.log(numbers); // [1, 10, 100, 9]
```

To sort numbers, pass a compare function. `a - b` sorts smallest first; `b - a` sorts largest first:

```javascript
numbers.sort((a, b) => a - b);
console.log(numbers); // [1, 9, 10, 100]

students.sort((a, b) => a.credits - b.credits); // Leo, Maya, Ana
```

`sort()` changes the original array. To keep the original order, sort a copy. `slice()` with no arguments returns a copy:

```javascript
const original = [3, 1, 2];
const sorted = original.slice().sort((a, b) => a - b);

console.log(sorted);   // [1, 2, 3]
console.log(original); // [3, 1, 2]
```

---

## split() and join()

`split()` breaks a string into an array, and `join()` turns an array back into a string:

```javascript
const parts = "red,green,blue".split(",");
console.log(parts);            // ["red", "green", "blue"]
console.log(parts.join(" / ")); // "red / green / blue"
```

---

## Sets

A Set holds unique values. Adding a value that is already there does nothing.

```javascript
const tags = new Set();
tags.add("js");
tags.add("css");
tags.add("js"); // ignored

console.log(tags.size);        // 2, note size, not length
console.log(tags.has("css"));  // true
tags.delete("css");
```

The most common use is removing duplicates from an array. `Array.from()` turns the Set back into an array:

```javascript
const days = ["Mon", "Tue", "Mon", "Wed"];
const uniqueDays = new Set(days);

console.log(uniqueDays.size);        // 3
console.log(Array.from(uniqueDays)); // ["Mon", "Tue", "Wed"]
```

Sets can be looped with `for...of` or `forEach()`, and they keep values in the order they were first added.

---

## Maps

A Map stores key and value pairs. Use one when the keys come from your data, such as book titles, rather than being fixed property names like `name` or `price`.

```javascript
const stock = new Map();

stock.set("apples", 12);
stock.set("pears", 5);

console.log(stock.get("apples")); // 12
console.log(stock.has("kiwis"));  // false
console.log(stock.size);          // 2
```

A Map's `forEach()` passes the value first, then the key:

```javascript
stock.forEach((count, fruit) => {
  console.log(`${fruit}: ${count}`);
});
```

### The counting pattern

A common job for a Map is counting. If the key exists, add one; otherwise start at one:

```javascript
const answers = ["yes", "no", "yes"];
const counts = new Map();

answers.forEach((answer) => {
  if (counts.has(answer)) {
    counts.set(answer, counts.get(answer) + 1);
  } else {
    counts.set(answer, 1);
  }
});

console.log(counts.get("yes")); // 2
```

---

## Exercise: Northside Book Club Reading List

The Northside Book Club wants a page for this season's reading list. Members should be able to see every book, filter by genre, and vote for the book they want to read next. The exercise uses an array of objects for the books, a Set for the genres, and a Map for the votes.

You are given `index.html` and `styles.css`. You will write `books.js`.

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
  </header>

  <main>
    <div id="genre-buttons"></div>
    <ul id="book-list"></ul>

    <h2>Votes</h2>
    <ul id="vote-list"></ul>
  </main>

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

#stats {
  display: flex;
  gap: 24px;
  font-weight: bold;
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
  padding: 6px 14px;
  border-radius: 16px;
  font-size: 0.9em;
}
```

The book titles and authors in this exercise are fictional.

### Step 1: Store the Books

Create `books.js` and store the reading list as an array of objects. Then log a few values to check the array.

```javascript
// books.js

const books = [
  { title: "The Silent Orchard", author: "Lena Hart", genre: "Mystery", pages: 312 },
  { title: "Paper Moons", author: "Omar Reyes", genre: "Fantasy", pages: 458 },
  { title: "Field Notes", author: "Priya Nand", genre: "Nonfiction", pages: 224 },
  { title: "The Last Ferry", author: "Sam Okafor", genre: "Mystery", pages: 276 },
  { title: "Glass Harbor", author: "Ines Duarte", genre: "Fantasy", pages: 390 },
  { title: "Small Habits", author: "Theo Lund", genre: "Nonfiction", pages: 198 }
];

console.log(books.length);
console.log(books[0].title);
console.log(books.map((book) => book.title));
```

Checkpoint: Open the page with Live Server. The Console shows `6`, then "The Silent Orchard", then an array of all six titles. Delete the three `console.log()` lines before moving on.

### Step 2: Display the Books

Write a `renderBooks()` function that builds one list item per book with `createElement()` and `textContent`. Each item holds the title in an `h3` and the details in a `span`, styled by the `.book` and `.book-info` classes.

```javascript
const bookList = document.querySelector("#book-list");

function renderBooks(list) {
  bookList.innerHTML = "";

  list.forEach((book) => {
    const item = document.createElement("li");
    item.classList.add("book");

    const info = document.createElement("div");
    info.classList.add("book-info");

    const title = document.createElement("h3");
    title.textContent = book.title;

    const details = document.createElement("span");
    details.textContent = `${book.author} | ${book.genre} | ${book.pages} pages`;

    info.appendChild(title);
    info.appendChild(details);

    item.appendChild(info);
    bookList.appendChild(item);
  });
}

renderBooks(books);
```

`renderBooks()` clears the list first, so calling it again redraws the list instead of adding a second copy.

Checkpoint: All six books appear as white cards, each with its title, author, genre, and page count.

### Step 3: Build Genre Buttons from a Set

The genre buttons are not typed into the HTML: if the club adds a book in a new genre, a button appears automatically. Use `map()` to collect every genre and a Set to keep only the unique ones.

```javascript
const genreButtons = document.querySelector("#genre-buttons");

function addGenreButton(label) {
  const button = document.createElement("button");
  button.type = "button";
  button.textContent = label;
  genreButtons.appendChild(button);
}

const genres = new Set(books.map((book) => book.genre));

addGenreButton("All");
genres.forEach((genre) => addGenreButton(genre));
```

Checkpoint: Four buttons appear: All, Mystery, Fantasy, Nonfiction. Mystery appears twice in the data but gets only one button.

### Step 4: Filter by Genre

Make each button show only the books in its genre. Add a click listener inside `addGenreButton()`, just before `genreButtons.appendChild(button);`:

```javascript
  button.addEventListener("click", () => {
    if (label === "All") {
      renderBooks(books);
    } else {
      renderBooks(books.filter((book) => book.genre === label));
    }
  });
```

Checkpoint: Clicking Mystery shows only The Silent Orchard and The Last Ferry. Clicking All brings back all six.

### Step 5: Count Votes with a Map

Each book gets a Vote button, and votes are counted in a Map keyed by book title. Add the Map and an `addVote()` function that uses the counting pattern:

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

Inside `renderBooks()`, add a Vote button to each item, just after `item.appendChild(info);`:

```javascript
    const voteButton = document.createElement("button");
    voteButton.type = "button";
    voteButton.textContent = "Vote";
    voteButton.addEventListener("click", () => addVote(book.title));
    item.appendChild(voteButton);
```

Checkpoint: Click Vote on Paper Moons twice. The Console shows the Map with Paper Moons mapped to 2. Click Fantasy, vote for Paper Moons again, and the count becomes 3: the Map lives outside `renderBooks()`, so votes survive filtering.

### Step 6: Show the Votes on the Page

Write a `showVotes()` function that loops through the Map, and call it from `addVote()` in place of `console.log(votes);`.

```javascript
const voteList = document.querySelector("#vote-list");

function showVotes() {
  voteList.innerHTML = "";

  votes.forEach((count, title) => {
    const item = document.createElement("li");
    item.textContent = `${title}: ${count}`;
    voteList.appendChild(item);
  });
}
```

Checkpoint: Vote for Paper Moons twice and Field Notes once. The Votes section shows "Paper Moons: 2" and "Field Notes: 1".

### Solution

```javascript
// books.js

const books = [
  { title: "The Silent Orchard", author: "Lena Hart", genre: "Mystery", pages: 312 },
  { title: "Paper Moons", author: "Omar Reyes", genre: "Fantasy", pages: 458 },
  { title: "Field Notes", author: "Priya Nand", genre: "Nonfiction", pages: 224 },
  { title: "The Last Ferry", author: "Sam Okafor", genre: "Mystery", pages: 276 },
  { title: "Glass Harbor", author: "Ines Duarte", genre: "Fantasy", pages: 390 },
  { title: "Small Habits", author: "Theo Lund", genre: "Nonfiction", pages: 198 }
];

const bookList = document.querySelector("#book-list");
const genreButtons = document.querySelector("#genre-buttons");
const voteList = document.querySelector("#vote-list");

const votes = new Map();

function renderBooks(list) {
  bookList.innerHTML = "";

  list.forEach((book) => {
    const item = document.createElement("li");
    item.classList.add("book");

    const info = document.createElement("div");
    info.classList.add("book-info");

    const title = document.createElement("h3");
    title.textContent = book.title;

    const details = document.createElement("span");
    details.textContent = `${book.author} | ${book.genre} | ${book.pages} pages`;

    info.appendChild(title);
    info.appendChild(details);

    const voteButton = document.createElement("button");
    voteButton.type = "button";
    voteButton.textContent = "Vote";
    voteButton.addEventListener("click", () => addVote(book.title));

    item.appendChild(info);
    item.appendChild(voteButton);

    bookList.appendChild(item);
  });
}

function addGenreButton(label) {
  const button = document.createElement("button");
  button.type = "button";
  button.textContent = label;

  button.addEventListener("click", () => {
    if (label === "All") {
      renderBooks(books);
    } else {
      renderBooks(books.filter((book) => book.genre === label));
    }
  });

  genreButtons.appendChild(button);
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
  voteList.innerHTML = "";

  votes.forEach((count, title) => {
    const item = document.createElement("li");
    item.textContent = `${title}: ${count}`;
    voteList.appendChild(item);
  });
}

const genres = new Set(books.map((book) => book.genre));

addGenreButton("All");
genres.forEach((genre) => addGenreButton(genre));

renderBooks(books);
```

**Challenge:** Add a Sort by Pages button that shows all books from shortest to longest, without changing the order of the original `books` array.

Add the button to `index.html`, right after the `#genre-buttons` div:

```html
    <button id="sort-pages" type="button">Sort by Pages</button>
```

In `books.js`, add a listener that sorts a copy:

```javascript
document.querySelector("#sort-pages").addEventListener("click", () => {
  const sorted = books.slice().sort((a, b) => a.pages - b.pages);
  renderBooks(sorted);
});
```

Click Sort by Pages, then click All: the original order returns. Now remove `.slice()` and try again. This time All also shows the sorted order, because `sort()` changed `books` itself. Put `.slice()` back.

---

## What Is JSON?

In Week 9 we saw what happens when we store anything other than a simple value:

```javascript
localStorage.setItem("test-list", ["a", "b"]);
console.log(localStorage.getItem("test-list")); // "a,b", no longer an array

localStorage.setItem("test-user", { name: "Maya" });
console.log(localStorage.getItem("test-user")); // "[object Object]", the data is gone
```

We need a way to turn an array or object into a string without losing its structure. That format is JSON, short for JavaScript Object Notation. JSON is text that looks almost exactly like a JavaScript object or array:

```json
{
  "name": "Maya",
  "credits": 45,
  "graduated": false,
  "courses": ["ITC 320", "BIO 101"]
}
```

JSON is also the most common format for sending data between a browser and a server, which is why it returns in the second half of today.

JSON is stricter than JavaScript:

- Property names and strings must use double quotes.
- No trailing commas and no comments.
- Values can only be strings, numbers, `true`/`false`, `null`, arrays, and objects. Functions and `undefined` are not allowed.

---

## JSON.stringify() and JSON.parse()

`JSON.stringify()` turns an array or object into a JSON string. `JSON.parse()` turns it back:

```javascript
const student = { name: "Maya", courses: ["ITC 320", "BIO 101"] };

const text = JSON.stringify(student);
console.log(text);        // {"name":"Maya","courses":["ITC 320","BIO 101"]}
console.log(typeof text); // "string"

const copy = JSON.parse(text);
console.log(copy.courses.length); // 2
```

`JSON.parse()` also fixes the `"false"` trap from Week 9, because it turns `"false"` back into the real boolean `false`.

Methods are dropped by `JSON.stringify()`, because JSON stores data, not behavior. Sets and Maps are not converted either, so turn them into arrays with `Array.from()` before saving, and turn them back after loading:

```javascript
const votes = new Map([["Paper Moons", 2]]);

const text = JSON.stringify(Array.from(votes));
console.log(text); // [["Paper Moons",2]]

const restored = new Map(JSON.parse(text));
console.log(restored.get("Paper Moons")); // 2
```

If the text is not valid JSON, `JSON.parse()` throws a `SyntaxError`. Whenever the text comes from somewhere you do not control, wrap the parse in `try...catch` from Week 5.

---

## Saving Arrays in Web Storage

Saving an array is a round trip:

1. Save: `JSON.stringify()` the array, then `setItem()`.
2. Load: `getItem()`, then `JSON.parse()`.

```javascript
const FAVORITES_KEY = "northside-favorites";

function saveFavorites(list) {
  localStorage.setItem(FAVORITES_KEY, JSON.stringify(list));
}
```

In the DevTools Application panel, the stored value appears as text such as `["Paper Moons","Field Notes"]`. The key uses a project prefix for the same reason as in Week 9: all your GitHub Pages projects share one origin.

### Loading safely

Three things can go wrong when loading: nothing has been saved yet (`getItem()` returns `null`), the stored text is not valid JSON, or it is valid JSON but not an array. `Array.isArray()` checks the last case; `typeof` cannot, because it reports arrays as `"object"`.

```javascript
function loadFavorites() {
  try {
    const saved = localStorage.getItem(FAVORITES_KEY);

    if (saved === null) {
      return [];
    }

    const parsed = JSON.parse(saved);

    if (!Array.isArray(parsed)) {
      return [];
    }

    return parsed;
  } catch (error) {
    console.warn("Saved favorites could not be read:", error);
    return [];
  }
}
```

Every failure returns an empty array, so the rest of the program can always use `push()` or `forEach()` without checking again.

To update a stored array, change the array in memory, then save the whole array again. `localStorage` has no `push()` of its own.

---

## What Is AJAX?

In Week 1, every request loaded a whole new page. AJAX lets JavaScript send a request in the background and use the response to update part of the page, without reloading. Search suggestions, new chat messages, and "Load more" buttons all use AJAX. The name originally stood for Asynchronous JavaScript and XML, but today the data is almost always JSON.

AJAX involves a server, but everything we write runs in the browser. A server-side course teaches how to build the program that sends the JSON. We write the client-side half: sending the request, waiting for the response, and putting the data on the page. We use `fetch()`, the modern built-in browser function. Next week's jQuery has its own AJAX methods.

### Setting up the demo files

`fetch()` needs a real file to request, so the examples in the rest of this section use a small demo folder. Create a folder named `ajax-demo` with these two files, and open `index.html` with Live Server.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>AJAX Demo</title>
</head>
<body>
  <h1>AJAX Demo</h1>
  <p>Open the Console to see the results.</p>
  <script src="demo.js"></script>
</body>
</html>
```

Given: `books.json`

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

Put each example below into a file named `demo.js` in the same folder, replacing the previous example, and reload the page to run it.

---

## async and await

A network request takes time. If JavaScript stopped and waited, the page would freeze. Instead, `fetch()` starts the request and immediately returns a Promise: an object that stands for a result that will arrive later.

To wait for a Promise without freezing the page, use `async` and `await`:

- Put `async` in front of a function to mark it as asynchronous.
- Inside it, put `await` in front of a Promise. The function pauses at that line until the result arrives, while the rest of the page keeps running.

```javascript
async function showMessages() {
  console.log("1. Before fetch");
  const response = await fetch("books.json");
  console.log("2. Response arrived");
}

showMessages();
console.log("3. Outside the function");
```

The output order is 1, 3, 2. The code after the function call ran while the request was still in progress.

Two rules:

- `await` only works inside an `async` function.
- If the request fails, `await` throws an error, so `try...catch` from Week 5 handles it.

---

## Fetching a Local JSON File

The simplest AJAX request loads a `.json` file from the same folder as the page. It takes two `await` steps:

```javascript
async function loadBooks() {
  const response = await fetch("books.json");
  const books = await response.json();

  console.log(books.length);   // 6
  console.log(books[0].title); // "The Silent Orchard"
}

loadBooks();
```

1. `await fetch()` sends the request and gives back a Response object.
2. `await response.json()` reads the body and parses it as JSON, the same job as `JSON.parse()`.

Open your page through Live Server. If you open the HTML by double-clicking it, the address starts with `file:///`, and the browser blocks `fetch()`. On GitHub Pages (Week 7), `fetch()` works normally, but file names are case-sensitive.

---

## Handling Fetch Errors

A request can fail in two ways:

| What happened | What fetch() does |
| --- | --- |
| No network connection, or the server cannot be reached | Throws an error at `await` |
| The server replies with an error status, such as 404 | Does not throw; `response.ok` is `false` |

A 404 does not count as an error to `fetch()`, because the server did reply. Check `response.ok` and use `throw` from Week 5, so both failures end up in the same `catch`:

```javascript
async function loadBooks() {
  try {
    const response = await fetch("books.json");

    if (!response.ok) {
      throw new Error(`Request failed with status ${response.status}`);
    }

    const books = await response.json();
    console.log(`Loaded ${books.length} books`);
  } catch (error) {
    console.error("Could not load books:", error.message);
  }
}

loadBooks();
```

The Console shows "Loaded 6 books". To test the error path, change `"books.json"` to `"book.json"`, a file that does not exist, and reload. The Console shows "Could not load books: Request failed with status 404".

---

## Calling a Public Web API

A web API is a URL that returns data instead of a web page. We use the Open Library Search API, which returns book information as JSON and needs no account or key. Paste this into your browser's address bar:

```text
https://openlibrary.org/search.json?title=harbor&limit=3
```

The response is an object with a `docs` property, which is an array of book objects. Each book has properties such as `title` and `author_name`. The exact results depend on the Open Library catalog on the day you try it.

To build the query string from user input, use `URLSearchParams` from Week 9. It encodes spaces and special characters correctly:

```javascript
const params = new URLSearchParams({ title: "glass harbor", limit: 5 });
console.log(params.toString()); // title=glass+harbor&limit=5

const url = `https://openlibrary.org/search.json?${params.toString()}`;
```

The request itself works exactly like loading a local file: `fetch()`, check `response.ok`, then `response.json()`. This example runs in the same demo folder:

```javascript
async function searchTitles(query) {
  const params = new URLSearchParams({ title: query, limit: 3 });

  try {
    const response = await fetch(`https://openlibrary.org/search.json?${params.toString()}`);

    if (!response.ok) {
      throw new Error(`Search failed with status ${response.status}`);
    }

    const data = await response.json();

    data.docs.forEach((book) => {
      console.log(book.title);
    });
  } catch (error) {
    console.error(error.message);
  }
}

searchTitles("harbor");
```

The Console shows up to three book titles. This request needs an internet connection, since the data comes from Open Library's server.

Data from someone else's API is not guaranteed:

- Some records have no `author_name`, so provide a fallback such as "Unknown author".
- `author_name` is an array, because a book can have several authors. Use `join(", ")` to display it.
- `docs` may be empty when nothing matches, so check its `length`.
- Always place API text on the page with `textContent`, not `innerHTML`. Week 14 explains why.

### Cross-origin requests and CORS

A request from your site to `openlibrary.org` is a cross-origin request. Browsers only allow it if the other server gives permission, using a mechanism called CORS. Open Library gives that permission. If another API produces a Console error mentioning "CORS", the problem is not your code; the server does not allow browser requests, so choose a different API.

---

## Exercise: Reading List with Open Library

The Northside Book Club likes the reading list page from the first exercise, but typing every book into `books.js` is slow. In this exercise you change the page so the books come from the Open Library web API, and the votes are saved so they survive a reload.

Make a copy of your Reading List folder (`index.html`, `styles.css`, and `books.js`) and work in the copy. Open it with Live Server. Most of your code stays the same: `renderBooks()`, `addVote()`, and `showVotes()` keep working. What changes is where the books come from.

In `index.html`, replace the `<div id="genre-buttons"></div>` line with a search box. The `#controls` rule already in `styles.css` lines it up:

```html
    <div id="controls">
      <input id="search-input" type="search" placeholder="Search by title">
      <button id="search-button" type="button">Search</button>
    </div>
```

Add this rule to the end of `styles.css`:

```css
#search-input {
  width: 60%;
  padding: 7px 10px;
  border: 1px solid #3d5a80;
  border-radius: 4px;
}
```

### Step 1: Fetch Books from Open Library

At the top of `books.js`, delete the hard-coded `books` array. Then add an `async` function that searches Open Library and logs the results. The `fields` parameter asks for only the title and author of each book, which keeps the response small.

```javascript
async function loadBooks(query) {
  const params = new URLSearchParams({
    title: query,
    fields: "title,author_name",
    limit: 6
  });

  try {
    const response = await fetch(`https://openlibrary.org/search.json?${params.toString()}`);

    if (!response.ok) {
      throw new Error(`Search failed with status ${response.status}`);
    }

    const data = await response.json();
    console.log(data.docs);
  } catch (error) {
    console.error(error);
  }
}
```

At the bottom of the file, delete the genre code (`const genres = ...`, `addGenreButton("All");`, and the `genres.forEach(...)` line) and the `renderBooks(books);` call. Replace them with:

```javascript
loadBooks("harbor");
```

You can also delete the `addGenreButton()` function and the `genreButtons` variable, since this page no longer uses them.

Checkpoint: Reload the page. The Console shows an array of up to six objects, each with a `title` and an `author_name` array.

### Step 2: Convert the Results into Books

`renderBooks()` expects objects with `title` and `author` properties, but Open Library gives `title` and `author_name`, and `author_name` is an array that is sometimes missing. Use `map()` to turn each result into the shape `renderBooks()` expects.

Replace `console.log(data.docs);` with:

```javascript
    const books = data.docs.map((doc) => {
      let author = "Unknown author";
      if (Array.isArray(doc.author_name)) {
        author = doc.author_name.join(", ");
      }

      return { title: doc.title, author: author };
    });

    console.log(books);
```

Checkpoint: The Console now shows an array of objects with `title` and `author`. Any book without an author shows "Unknown author".

### Step 3: Display the Books

The books no longer have a genre or a page count, so the details line in `renderBooks()` only needs the author. Change this line:

```javascript
    details.textContent = `${book.author} | ${book.genre} | ${book.pages} pages`;
```

to:

```javascript
    details.textContent = book.author;
```

Then, in `loadBooks()`, replace `console.log(books);` with:

```javascript
    renderBooks(books);
```

`renderBooks()` is called inside `loadBooks()`, after the `await` lines, because the books do not exist until the response has arrived.

Checkpoint: Up to six book cards appear, each with a title, an author, and a Vote button. Voting still works exactly as in the first exercise.

### Step 4: Add the Search Box

Let members search for any title. Add this near the top of the file:

```javascript
const searchInput = document.querySelector("#search-input");
const searchButton = document.querySelector("#search-button");

searchButton.addEventListener("click", () => {
  loadBooks(searchInput.value.trim());
});
```

Checkpoint: Search for "garden". The cards are replaced by new results. Votes you cast before the search are still listed, because the votes Map does not depend on which books are showing.

### Step 5: Save the Votes with JSON

A Map cannot be saved directly, so turn it into an array with `Array.from()` first. Add a key near the top of the file:

```javascript
const VOTES_KEY = "northside-votes";
```

In `addVote()`, add this line just before `showVotes();`:

```javascript
  localStorage.setItem(VOTES_KEY, JSON.stringify(Array.from(votes)));
```

Checkpoint: Vote for two books. In the DevTools Application panel, `northside-votes` holds an array of pairs, such as `[["Glass Harbor",2],["The Harbor",1]]`. Now reload: the Votes list is empty, because the votes are saved but never loaded.

### Step 6: Load the Votes on Start-up

Add a `loadVotes()` function with the three guards from the theory section. It turns the saved array of pairs back into a Map with `new Map()`.

```javascript
function loadVotes() {
  try {
    const saved = localStorage.getItem(VOTES_KEY);

    if (saved === null) {
      return new Map();
    }

    const parsed = JSON.parse(saved);

    if (!Array.isArray(parsed)) {
      return new Map();
    }

    return new Map(parsed);
  } catch (error) {
    console.warn("Saved votes could not be read:", error);
    return new Map();
  }
}
```

Change `const votes = new Map();` to:

```javascript
const votes = loadVotes();
```

Finally, add `showVotes();` just above `loadBooks("harbor");` at the bottom of the file, so saved votes appear as soon as the page opens.

Checkpoint: Vote for two books and reload. The votes are still there. Then, in the Console, run `localStorage.setItem("northside-votes", "oops")` and reload. The Votes list is empty and nothing crashes.

### Solution

```javascript
// books.js

const VOTES_KEY = "northside-votes";

const bookList = document.querySelector("#book-list");
const voteList = document.querySelector("#vote-list");
const searchInput = document.querySelector("#search-input");
const searchButton = document.querySelector("#search-button");

const votes = loadVotes();

function renderBooks(list) {
  bookList.innerHTML = "";

  list.forEach((book) => {
    const item = document.createElement("li");
    item.classList.add("book");

    const info = document.createElement("div");
    info.classList.add("book-info");

    const title = document.createElement("h3");
    title.textContent = book.title;

    const details = document.createElement("span");
    details.textContent = book.author;

    info.appendChild(title);
    info.appendChild(details);

    const voteButton = document.createElement("button");
    voteButton.type = "button";
    voteButton.textContent = "Vote";
    voteButton.addEventListener("click", () => addVote(book.title));

    item.appendChild(info);
    item.appendChild(voteButton);

    bookList.appendChild(item);
  });
}

function addVote(title) {
  if (votes.has(title)) {
    votes.set(title, votes.get(title) + 1);
  } else {
    votes.set(title, 1);
  }

  localStorage.setItem(VOTES_KEY, JSON.stringify(Array.from(votes)));
  showVotes();
}

function showVotes() {
  voteList.innerHTML = "";

  votes.forEach((count, title) => {
    const item = document.createElement("li");
    item.textContent = `${title}: ${count}`;
    voteList.appendChild(item);
  });
}

function loadVotes() {
  try {
    const saved = localStorage.getItem(VOTES_KEY);

    if (saved === null) {
      return new Map();
    }

    const parsed = JSON.parse(saved);

    if (!Array.isArray(parsed)) {
      return new Map();
    }

    return new Map(parsed);
  } catch (error) {
    console.warn("Saved votes could not be read:", error);
    return new Map();
  }
}

async function loadBooks(query) {
  const params = new URLSearchParams({
    title: query,
    fields: "title,author_name",
    limit: 6
  });

  try {
    const response = await fetch(`https://openlibrary.org/search.json?${params.toString()}`);

    if (!response.ok) {
      throw new Error(`Search failed with status ${response.status}`);
    }

    const data = await response.json();

    const books = data.docs.map((doc) => {
      let author = "Unknown author";
      if (Array.isArray(doc.author_name)) {
        author = doc.author_name.join(", ");
      }

      return { title: doc.title, author: author };
    });

    renderBooks(books);
  } catch (error) {
    console.error(error);
  }
}

searchButton.addEventListener("click", () => {
  loadBooks(searchInput.value.trim());
});

showVotes();
loadBooks("harbor");
```

**Challenge:** Add a Reset Votes button that clears every vote, both on the page and in storage.

Add the button to `index.html`, just after the `<h2>Votes</h2>` heading:

```html
    <button id="reset-votes" type="button">Reset Votes</button>
```

In `books.js`, add a listener. `clear()` empties the Map, and `removeItem()` from Week 9 deletes the saved copy:

```javascript
document.querySelector("#reset-votes").addEventListener("click", () => {
  votes.clear();
  localStorage.removeItem(VOTES_KEY);
  showVotes();
});
```

Test it: vote for a few books, click Reset Votes, and reload. The Votes list stays empty, and `northside-votes` is gone from the Application panel.

---

## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition, Mike Murach & Associates Inc., 2020.
  - Chapter 15 (How to work with arrays, sets, and maps)
  - Chapter 18 (How to work with AJAX)
- W3Schools, JavaScript Arrays: https://www.w3schools.com/js/js_arrays.asp
- W3Schools, JavaScript Sets: https://www.w3schools.com/js/js_sets.asp
- W3Schools, JavaScript Maps: https://www.w3schools.com/js/js_maps.asp
- W3Schools, JSON Introduction: https://www.w3schools.com/js/js_json.asp
- W3Schools, JavaScript Fetch API: https://www.w3schools.com/js/js_api_fetch.asp
- Open Library, Search API: https://openlibrary.org/dev/docs/api/search
