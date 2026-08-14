# Week 5: Testing and Debugging

## Why Testing and Debugging Matter

Every script you write will eventually break, either while you are writing it or after someone else starts using it. The goal is not to avoid mistakes entirely. That is not realistic. The goal is to get faster at finding a mistake once it happens and at understanding why the browser is behaving the way it is.

So far, when something went wrong in your code, you may have tracked it down by staring at the code, adding an alert(), or just guessing. This week introduces the tools professional developers actually use: reading error messages carefully, using the browser console, stepping through code with breakpoints, and handling errors on purpose with try...catch.

## Types of Errors

Not all bugs are the same, and the way you find them depends on which type you are dealing with.

### Syntax Errors

A syntax error means the JavaScript engine cannot even understand your code. This happens before your script runs at all. Common causes are a missing closing brace, a missing parenthesis, or a misspelled keyword.

```js
function greetUser(name {
  console.log("Hello, " + name);
}
```

The missing closing parenthesis after `name` means this code will not run at all. The console will point you to the line where it got confused, though sometimes that line is not exactly where the real problem is.

### Runtime Errors

A runtime error means your code is syntactically valid, but something goes wrong while it is actually executing. A very common example is trying to call a method on something that does not exist.

```js
const total = document.getElementById("totl").textContent;
```

If there is no element with the id `totl` (notice the typo), `getElementById` returns `null`, and trying to read `.textContent` from `null` throws a runtime error. The script stops right there.

### Logic Errors

A logic error is the trickiest kind. The code runs without any error message at all, but it produces the wrong result. There is nothing for the console to complain about because, as far as JavaScript is concerned, nothing went wrong.

```js
function isEligible(age) {
    return age > 18;
}

console.log("Is Eligible: " + isEligible(18));
```

This function will incorrectly return `false` for someone who is exactly 18. No error is thrown, so you will only catch this by testing the function with real values and checking the output against what you expected.

## Reading Error Messages in the Console

When the browser throws an error, it prints a message to the console with a few important pieces of information: the error type, a description of what went wrong, and the file name and line number where it happened.

```js
const price = undefined;
console.log(price.toFixed(2));
```

Open the console and you will see something like `TypeError: Cannot read properties of undefined (reading 'toFixed')`, along with a link to the exact line. The error type tells you the category of problem. TypeError usually means you tried to use a value in a way that does not match its type, such as calling a method on `undefined`.

Reading the error message fully, instead of just noticing that something turned red, is the single most useful debugging habit you can build.

## Debugging with console Methods

`console.log()` is not the only tool available. A few other console methods make debugging faster.

```js
console.log("Order total:", total);
console.warn("Discount code not recognized");
console.error("Payment could not be processed");
```

`console.warn()` and `console.error()` print with extra visual styling in the console, which makes them easier to spot than a plain `console.log()` when you are scanning through a lot of output.

`console.table()` is especially useful when logging an object, since it formats the properties into a readable table instead of a single line.

```js
const student = { name: "Jordan", year: "Sophomore", gpa: 3.6 };
console.table(student);
```

## Debugging with the Browser Sources Panel

Console logging works well, but sometimes you need to pause your code mid-execution and inspect exactly what every variable holds at that moment. That is what the Sources panel in Developer Tools is for.

### Setting a Breakpoint

Open Developer Tools, go to the Sources tab, find your JavaScript file, and click on a line number to set a breakpoint. The next time that line is about to run, the browser pauses execution completely.

### Stepping Through Code

Once paused, you have three main controls.

Step over moves to the next line without entering any function calls on that line. Step into moves inside a function call so you can watch what happens inside it. Step out finishes the current function and returns to wherever it was called from.

### Watching Variables and the Call Stack

While paused, the Scope panel shows the current value of every variable in reach, and the Call Stack panel shows the chain of function calls that led to this point. Both update live as you step through the code.

### The debugger Statement

You can also pause execution directly from your code, without clicking a line number in the browser, by inserting the word `debugger`.

```js
function calculateTotal(price, quantity) {
    let subtotal = price * quantity;
    let tax = subtotal * 0.06;
    let total = subtotal + tax;

    return total;
}

let price = 20;
let quantity = 3;

let result = calculateTotal(price, quantity);

console.log("Total:", result.toFixed(2));
```

Try selecting `let tax = subtotal * 0.06;` from Developer Tools under Source. When this line runs, execution pauses right there, exactly as if you had clicked to set a breakpoint. This is convenient when you already know roughly where the problem is.

## Handling Errors with try...catch...finally

So far, when an error happens, the script simply stops. Below is one such example:

```js
const user = null;
console.log("Starting script");
console.log(user.name);
console.log("This line never runs");
```
Sometimes you want your code to notice that something went wrong and respond gracefully instead of crashing. Code inside the `try` block runs normally. If any statement inside it throws an error, execution immediately jumps to the `catch` block instead of continuing in `try`, and the rest of the script keeps running afterward instead of stopping completely.

```js
const user = null;

try {
  console.log("Starting script");
  console.log(user.name);
  console.log("This line never runs");
} catch (error) {
  console.error("Caught an error:", error.message);
}

console.log("Script keeps going after the catch block");
```

A `finally` block, if included, always runs after `try` and `catch`, whether or not an error occurred. It is commonly used for cleanup steps that need to happen no matter what.

```js
const user = null;

try {
  console.log(user.name);
} catch (error) {
  console.error("Caught an error:", error.message);
} finally {
  console.log("This always runs, error or not");
}
```

### Throwing Your Own Errors

You are not limited to catching errors the browser generates on its own. You can create and throw your own with the `throw` keyword, which is useful for enforcing rules in your own code.

```js
function divide(a, b) {
    try {
        if (b === 0) {
            throw new Error("Cannot divide by zero")
        }
        const result = a/b;
        console.log("Result: " + result);

    } catch (error) {
        console.error("Caught an error.", error.message);
        
    }
}

divide(4,2);
```

`new Error("Cannot divide by zero")` creates an Error object with a `message` property holding that text. When this function is called from inside a `try` block, the thrown error is caught and its `.message` can be read in the `catch` block, exactly like a built in error.

## Defensive Coding

try...catch handles errors after they happen. Defensive coding tries to prevent invalid situations from happening in the first place, by checking values before you use them.

```js
function showUserName(user) {
    if (!user) {
        console.warn("No user provided.")
        return;
    }
    document.getElementById("para").textContent = user.name;
}

const user = {name: "Steve"};

showUserName(user);
```

The `if (!user)` check is called a guard clause. It stops the function early if the value it depends on is missing, instead of letting the rest of the function run into a runtime error a few lines down. However, below will use the provided warning a user is `null`;
```js
const user = null;
```

## Exercise: Coffee Order Tracker (with Bug Resistance)
You are provided with HTML and CSS for a webpage for Coffee Order Tracker that we have been working on. The webpage has a bug in its Javascript. Make sure you improve the code, so that it does not break. Take all necessary measure by adopting defensive coding.

`index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Corner Cafe | Coffee Order Tracker</title>
    <link rel="stylesheet" href="style.css" />
  </head>

  <body>
    <main class="container">
      <div class="logo">☕</div>

      <h1>Corner Cafe</h1>

      <p class="subtitle">Tap an item to add it to your order.</p>

      <section class="menu">
        <button id="btn-coffee">
          ☕ Coffee
          <span class="price">$3</span>
        </button>

        <button id="btn-latte">
          🥛 Latte
          <span class="price">$5</span>
        </button>

        <button id="btn-muffin">
          🧁 Muffin
          <span class="price">$4</span>
        </button>
      </section>

      <hr class="divider" />

      <button id="summary-btn" class="summary-btn">View Order Summary</button>

      <div id="order-summary">
        <h2>☕ Your Order</h2>
        <p id="total-order" class="empty-message">No items added yet.</p>
      </div>

      <button id="clear-btn" class="summary-btn">Clear Order</button>

      <p class="footer">Thank you for visiting Corner Cafe!</p>
    </main>

    <script src="week_5.js"></script>
  </body>
</html>

```

`styles.css`

```css
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, Helvetica, sans-serif;
    min-height: 100vh;
    background: #f5f1eb;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #2f241f;
}

.container {
    width: 90%;
    max-width: 650px;
    background: white;
    padding: 40px;
    border-radius: 16px;
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.12);
    text-align: center;
}

.logo {
    font-size: 48px;
    margin-bottom: 10px;
}

h1 {
    font-size: 36px;
    margin-bottom: 8px;
    color: #4b2e22;
}

.subtitle {
    color: #766860;
    margin-bottom: 30px;
    font-size: 16px;
}

.menu {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 15px;
    margin-bottom: 30px;
}

.menu button {
    border: 2px solid #e0d4cc;
    background: #faf8f6;
    color: #3d2b24;
    padding: 20px 10px;
    border-radius: 12px;
    font-size: 16px;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.2s ease;
}

.menu button:hover {
    background: #f1e5dc;
    border-color: #a56b4f;
    transform: translateY(-2px);
}

.menu button:active {
    transform: translateY(0);
}

.price {
    display: block;
    margin-top: 6px;
    font-size: 14px;
    font-weight: normal;
    color: #8b6f61;
}

.divider {
    border: none;
    border-top: 1px solid #e5ddd8;
    margin: 10px 0 25px;
}

.summary-btn {
    width: 100%;
    padding: 16px;
    border: none;
    border-radius: 10px;
    background: #4b2e22;
    color: white;
    font-size: 17px;
    font-weight: bold;
    cursor: pointer;
    transition: background 0.2s ease;
}

.summary-btn:hover {
    background: #684333;
}

/* Order Summary */

#order-summary {
    margin-top: 20px;
    padding: 20px;
    background: #faf8f6;
    border: 1px solid #e5ddd8;
    border-radius: 10px;
    text-align: left;
}

#order-summary h2 {
    margin-bottom: 15px;
    color: #4b2e22;
}

#order-summary p {
    margin-bottom: 8px;
}

#order-summary strong {
    display: block;
    margin-top: 15px;
    padding-top: 15px;
    border-top: 1px solid #e5ddd8;
    color: #4b2e22;
}

#total-order {
    white-space: pre-line;
}

.footer {
    margin-top: 25px;
    font-size: 13px;
    color: #998c85;
}

```
Here is the script but has bugs;

```js
let menu = {
    coffee: 3,
    latte: 5,
    muffin: 4
};

let order = {
    coffee: 0,
    latte: 0,
    muffin: 0
};

const total_order = document.getElementById("total-order");

function addToOrder(item) {
    order[item] = order[item] + 1;
    alert(item + " added to your order.");
}

function calculateTotal() {
    let total = 0;
    for (let item in order) {
        total = total + order[item] * mnu[item];
    }
    return total;
}

function showSummary() {
    let receipt = "Order Summary\n";

    for (let item in order) {
        if (order[item] > 0) {
            receipt += order[item] + " x " + item + "\n";
        }
    }

    receipt += "Total: $" + calculateTotal();

    total_order.textContent = receipt;
}

function clearOrder() {
    for (let item in order) {
        order[item] = 0;
    }

    total_order.textContent = "No items added yet.";
}

document.getElementById("btn-coffee").addEventListener("click", event => {
    addToOrder("coffee");
});

document.getElementById("btn-latte").addEventListener("click", event => {
    addToOrder("latte");
});

document.getElementById("btn-muffin").addEventListener("click", event => {
    addToOrder("muffin");
});

document.getElementById("summary-btn").addEventListener("click", event => {
    showSummary();
});

document.getElementById("clear-btn").addEventListener("click", event => {
    clearOrder();
});
```

Checkpoint: open the console before you click the button. What error do you see? Read the message carefully, including the line number it points to, before moving on.

### Solution

```js
let menu = {
    coffee: 3,
    latte: 5,
    muffin: 4
};

let order = {
    coffee: 0,
    latte: 0,
    muffin: 0
};

const total_order = document.getElementById("total-order");

function addToOrder(item) {
    if (!(item in menu)) {
        console.error("Tried to add an item not on the menu:", item);
        return;
    }

    order[item] = order[item] + 1;
    console.log("Added", item, ". New quantity:", order[item]);
    alert(item + " added to your order.");
}

function calculateTotal() {
    let total = 0;
    for (let item in order) {
        total = total + order[item] * menu[item];
    }
    return total;
}

function showSummary() {
    try {
        let receipt = "Order Summary\n";

        for (let item in order) {
            if (order[item] > 0) {
                receipt += order[item] + " x " + item + "\n";
            }
        }

        receipt += "Total: $" + calculateTotal();
        total_order.textContent = receipt;

        console.table(order);
    } catch (error) {
        console.error("Could not build the order summary:", error.message);
        total_order.textContent = "Something went wrong building your order summary.";
    } finally {
        console.log("Summary attempt finished.");
    }
}

function clearOrder() {
    for (let item in order) {
        order[item] = 0;
    }

    total_order.textContent = "No items added yet.";
}

document.getElementById("btn-coffee").addEventListener("click", event => {
    addToOrder("coffee");
});

document.getElementById("btn-latte").addEventListener("click", event => {
    addToOrder("latte");
});

document.getElementById("btn-muffin").addEventListener("click", event => {
    addToOrder("muffin");
});

document.getElementById("summary-btn").addEventListener("click", event => {
    showSummary();
});

document.getElementById("clear-btn").addEventListener("click", event => {
    clearOrder();
});
```

**Challenge:** Add a limit of 5 of any single item per order. If a customer tries to add a 6th, throw an error instead of silently updating the order, and catch it where the button click happens so the page shows a message instead of crashing.



## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition. 
  - Chapter 5: How to test and debug a JavaScript application.
- W3Schools Turotials
  - [JS Debugging](https://www.w3schools.com/js/js_debugging.asp)
  - [JS Throw and Try to Catch Errors](https://www.w3schools.com/js/js_errors.asp)
