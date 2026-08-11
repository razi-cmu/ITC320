# Week 2: Getting Started with JavaScript

## What Is JavaScript, and Why Do We Need It?

JavaScript (JS) is a programming language that runs inside the browser, and it's what lets a web page respond to what the user does: validating a form before it's submitted, showing/hiding content when a button is clicked, updating part of a page without reloading it, and so on.

A simple way to think about the three layers of a web page:

| Layer | Job | Example |
| --- | --- | --- |
| HTML | Structure/content | "There is a button here" |
| CSS | Presentation | "The button is blue and rounded" |
| JavaScript | Behavior | "When the button is clicked, do something" |

Learning JavaScript is less about learning "how to program" from zero, and more about learning this particular language's syntax and rules, plus how it interacts with a web page. One important thing that makes JS different from most languages is it was built to run inside a browser, reacting to a user's actions in real time. 


## Adding JavaScript to a Web Page

Just like CSS, there are three ways to add JavaScript to a page.

**1. Inline JavaScript**: JS code placed directly inside an HTML attribute. Generally avoided in real projects because it mixes behavior into your markup, but you'll see it in older code.

```html
<button onclick="alert('Hello!')">Click Me</button>
```

**2. Internal JavaScript**: JS written inside a `<script>` tag directly in the HTML file.

```html
<script>
  console.log("Hello from internal JS!");
</script>
```

**3. External JavaScript**: JS written in its own `.js` file and linked into the HTML, using the `src` attribute. This is the standard, professional approach, and it's the one we'll use for the rest of the course for separation of concerns, reusability, and easier maintenance.

```html
<script src="script.js"></script>
```

Where does the `<script>` tag go?:

- At the end of the `<body>`, right before `</body>`: this ensures the HTML is fully loaded before your script tries to interact with it.
- In the `<head>`, with the `defer` attribute like `<script src="script.js" defer></script>`: which tells the browser "download this now, but wait until the page is parsed before running it."

Either approach works; we'll use "end of body" for now since it's simpler.

## Browser Console

Before we write a single line of JS, let's meet the tool you'll use constantly for the rest of this course, the Developer Tools, built into every modern browser.

- Open it with `F12` or `Right-click → Inspect`.
- Click the Console tab.

The console lets you:
- See output your JavaScript code produces (via `console.log()`)
- See errors when something in your code is broken
- Type and run JavaScript directly, on the spot, for quick experiments

```js
console.log("This message shows up in the console.");
```

##  Variables

A variable is a named container for a value. You've used these in every language you've learned. JS just has its own keywords for declaring them.

JavaScript gives us three keywords to declare a variable:

| Keyword | Can be reassigned? | Notes |
| --- | --- | --- |
| `let` | Yes | Use this for values that will change |
| `const` | No | Use this for values that should never change |
| `var` | Yes | The old way (pre-2015).  |

```js
let studentName = "Alex";
const maxScore = 100;
```

Naming rules for variables:
- Must start with a letter, `_`, or `$`, not a number
- Can't contain spaces or most special characters
- Case-sensitive (`score` and `Score` are different variables)
- JavaScript convention is camelCase: `firstName`, `totalPrice`, `isLoggedIn`

## Data Types

Every value in JavaScript has a type. Unlike some languages, JS does not require you to declare a variable's type, it figures it out automatically based on the value you assign. This is called being dynamically typed.

The core primitive types you'll use constantly:

| Type | Example | Notes |
| --- | --- | --- |
| String | `"hello"` or `'hello'` | Text, wrapped in quotes |
| Number | `42`, `3.14` | JS has just one number type, no separate int/float |
| Boolean | `true`, `false` | Logical yes/no |
| Undefined | `let x;` | A variable that's been declared but not given a value |
| Null | `let x = null;` | Deliberately "no value" |

You can check a value's type at any time using the `typeof` operator:

```js
console.log(typeof "hello");   // "string"
console.log(typeof 42);        // "number"
console.log(typeof true);      // "boolean"
```

## Working with Strings

Strings are text. You can build them with single quotes, double quotes, or the modern, recommended way "template literals", using backticks (`` ` ``). Template literals let you embed variables directly inside the string using `${}`, instead of stitching pieces together with `+`.

```js
const firstName = "Alex";
const lastName = "Rivera";

// Old way: string concatenation
const greeting1 = "Hello, " + firstName + " " + lastName + "!";

// Modern way: template literal
const greeting2 = `Hello, ${firstName} ${lastName}!`;

console.log(greeting2); // Hello, Alex Rivera!
```

## Setting Up Our First Script

Let's build this together, step by step, saving and checking the console after each small change.

**Step 1: Create the files.**
Create `week2.html` and `week2.js` in the same folder.

```html
<!DOCTYPE html>
<html>
<head>
  <title>Week 2 - JS Basics</title>
</head>
<body>
  <h1>Week 2: JavaScript Basics</h1>

  <script src="week2.js"></script>
</body>
</html>
```

**Step 2: Confirm the link works.**
In `week2.js`:

```js
console.log("week2.js is connected!");
```

Save, refresh the browser, open the console, and confirm you see the message.

**Step 3: Declare a few variables.**

```js
const studentName = "Alex";
let currentScore = 85;
let isPassing = true;
```

Save and refresh (no visible output yet, that's expected, we haven't logged anything).

**Step 4: Log them using a template literal.**

```js
console.log(`${studentName} has a score of ${currentScore}.`);
console.log(`Passing status: ${isPassing}`);
```

Save, refresh, check the console.

**Step 5: Check some types.**

```js
console.log(typeof studentName);
console.log(typeof currentScore);
console.log(typeof isPassing);
```

## Operators

Operators let you work with values, do math, compare things, and combine logical conditions. Most of these will look familiar from prior programming experience; the syntax overlaps heavily with other C-family languages.

**Arithmetic operators:**

| Operator | Meaning |
| --- | --- |
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Remainder (modulus) |
| `**` | Exponent |

**Assignment operators:**

| Operator | Meaning | Equivalent to |
| --- | --- | --- |
| `=` | Assign | — |
| `+=` | Add and assign | `x = x + 1` |
| `-=` | Subtract and assign | `x = x - 1` |
| `++` | Increment by 1 | `x = x + 1` |
| `--` | Decrement by 1 | `x = x - 1` |

**Comparison operators**: these produce a Boolean (`true`/`false`):

| Operator | Meaning |
| --- | --- |
| `===` | Equal to (strict — checks value **and** type) |
| `!==` | Not equal to (strict) |
| `>` `<` `>=` `<=` | Greater/less than (or equal) |

A quick but important note: JavaScript also has `==` and `!=` ("loose" equality), which try to convert types before comparing (e.g. `"5" == 5` is `true`). This causes subtle bugs. 

**Logical operators**: used to combine Boolean conditions:

| Operator | Meaning |
| --- | --- |
| `&&` | AND: both sides must be true |
| \|\| | OR: at least one side must be true |
| `!` | NOT: flips true/false |


## Statements vs. Expressions

- An expression is anything that produces a value: `5 + 3`, `score > 60`, `"hi" + "there"`.
- A statement is a full instruction that performs an action, often built using expressions: `let total = 5 + 3;` or an entire `if` block.

## Conditional Statements

`if / else if / else` works exactly the way it does in the language(s) you've used before. The main difference is JavaScript's curly-brace syntax and the use of `===` for comparisons.

```js
const score = 85;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else {
  console.log("Grade: C or below");
}
```

`switch` is useful when you're comparing one value against several possible exact matches, instead of writing a long `else if` chain. Each case needs a `break` statement, or execution will "fall through" into the next case.

```js
const day = "Mon";

switch (day) {
  case "Mon":
    console.log("Start of the week");
    break;
  case "Fri":
    console.log("Almost the weekend");
    break;
  default:
    console.log("Midweek");
}
```

## Loops

Loops are used for repeating an action without repeating code. Here's how the three main loop types look in JavaScript.

`for` loop: best when you know how many times you want to repeat something.

```js
for (let i = 1; i <= 5; i++) {
  console.log(`Count: ${i}`);
}
```

The three parts inside the parentheses: starting point, condition to keep looping, and what happens after each pass are same structure you've seen in other languages.

`while` loop: best when you don't know in advance how many repetitions you'll need, only the condition that should keep it going.

```js
let count = 1;
while (count <= 5) {
  console.log(`Count: ${count}`);
  count++;
}
```

`do...while` loop: same as `while`, but guarantees the code runs at least once, because the condition is checked after the first pass.

```js
let attempts = 0;
do {
  console.log("Attempting...");
  attempts++;
} while (attempts < 3);
```

## Exercise
Create a file named `welcome.js` and link it to `welcome.html` (already provided). Your script should run automatically when the page loads, and do the following, in order:
- Use `prompt()` to ask the user for their name, and store it in a variable.
- Use `prompt()` to ask the user to pick their favorite number between 1 and 10.
- Check whether the number is even or odd, and build a message stating which one it is.
- Using a loop, build a countdown message that lists every number from 1 up to the favorite number, one per line.
- Use a single `alert()` to show the user's name, the even/odd message, and the full countdown, all in one final pop-up.

Here is the sample HTML provided to you:
```html
<!DOCTYPE html>
<html>
<head>
  <title>Welcome Page</title>
</head>
<body>
  <div class="card">
    <h1>Welcome!</h1>
    <p>This page comes alive with JavaScript — watch for the pop-ups.</p>
  </div>

  <script src="welcome.js"></script>
</body>
</html>
```

### Solution:

```js
const userName = prompt("Welcome! What's your name?");

const favoriteNumberText = prompt(`Hi ${userName}! Pick your favorite number between 1 and 10:`);
const favoriteNumber = Number(favoriteNumberText);

let numberMessage = "";
if (favoriteNumber % 2 === 0) {
  numberMessage = `${favoriteNumber} is an even number.`;
} else {
  numberMessage = `${favoriteNumber} is an odd number.`;
}


let countdownMessage = "";
for (let i = 1; i <= favoriteNumber; i++) {
    countdownMessage += `${i}\n`;
}

// Step 5: Show everything in one final message
alert(`Thanks, ${userName}!\n\n${numberMessage}\n\nHere's your countdown:\n${countdownMessage}`);
```

## References
- Murach's JavaScript and jQuery (4th Edition)
  - Chapter 2: Get started fast with JavaScript
  - Chapter 3: The essential JavaScript statements
- W3Schools JavaScript Tutorials
  - [Operators](https://www.w3schools.com/js/js_operators.asp)
  - [Conditions](https://www.w3schools.com/js/js_if.asp)
  - [Loops](https://www.w3schools.com/js/js_loops.asp)

