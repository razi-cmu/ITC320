# Week 3: JavaScript Objects, Functions / Events

## What Is a Function?

A function is a named block of code that performs a task. Instead of writing the same lines of code over and over, you write them once inside a function and then run that function whenever you need it. This is one of the core ideas behind writing clean, maintainable code.

Think of a function as a small machine. You give it some input, it does some work, and it may hand you back a result.

### Declaring and Calling a Function

The basic syntax for declaring a function looks like this:

```javascript
function functionName(parameter1, parameter2) {
  // code that runs when the function is called
}
```

Here is a simple example that does not take any input:

```javascript
function greetStudent() {
  console.log("Welcome to class!");
}
```

Declaring a function does not run it. You have to call it, meaning you use its name followed by parentheses:

```javascript
greetStudent();
```

### Parameters and Arguments

A parameter is a placeholder variable listed in the function declaration. An argument is the actual value you pass in when you call the function.

```javascript
function greetByName(name) {
  console.log("Welcome, " + name + "!");
}

greetByName("Maria");
greetByName("James");
```

In this example, `name` is the parameter, and `"Maria"` and `"James"` are the arguments used in each call.

A function can take more than one parameter, separated by commas:

```javascript
function displayFullName(firstName, lastName) {
  console.log(firstName + " " + lastName);
}

displayFullName("Ana", "Torres");
```

You can also give a parameter a default value, which is used if no argument is passed for it:

```javascript
function greetVisitor(name = "Guest") {
  console.log("Hello, " + name);
}

greetVisitor();          // Hello, Guest
greetVisitor("Priya");   // Hello, Priya
```

### The return Statement

So far, our functions have only displayed output. Often you want a function to calculate something and hand the result back to whatever code called it. That is what `return` does.

```javascript
function addNumbers(num1, num2) {
  let sum = num1 + num2;
  return sum;
}

let result = addNumbers(5, 8);
console.log(result); // 13
```

A few important points about `return`:

- As soon as a `return` statement runs, the function stops executing. Any code after it will not run.
- A function without a `return` statement still runs fine, it simply returns `undefined` if you try to use its result.
- The value returned can be stored in a variable, used in an expression, or passed directly into another function call.

```javascript
function squareNumber(num) {
  return num * num;
}

console.log(squareNumber(4) + squareNumber(3)); // 25
```

### Local Scope vs Global Scope

Scope determines where in your code a variable can be used. This connects directly to the `let` and `const` keywords from Week 2.

A variable declared inside a function is local to that function. It only exists while the function is running, and it cannot be accessed from outside the function.

```javascript
function calculateArea(width, height) {
  let area = width * height; // area is local to this function
  return area;
}

console.log(area); // Error, area is not defined out here
```

A variable declared outside of any function is global, meaning it can be accessed anywhere in your script, including inside functions.

```javascript
let taxRate = 0.06; // global variable

function calculateTotal(price) {
  return price + price * taxRate; // taxRate is visible here
}
```

General rule to follow: declare variables inside the function that uses them whenever possible. Relying too heavily on global variables makes code harder to track and debug as programs grow.

## What Is an Object?

Every data type you used in Week 2, numbers, strings, and booleans, stores one single value. An object lets you group multiple related values together under one name.

For example, instead of tracking a student's information in separate variables like this:

```javascript
let studentName = "Alex";
let studentAge = 20;
let studentMajor = "Computer Science";
```

you can group them into a single object:

```javascript
let student = {
  name: "Alex",
  age: 20,
  major: "Computer Science"
};
```

This is called an object literal. Each `key: value` pair is called a property. Properties are separated by commas, and the whole object is wrapped in curly braces.

### Accessing and Modifying Properties

There are two ways to access a property on an object.

Dot notation is the most common:

```javascript
console.log(student.name);  // Alex
console.log(student.age);   // 20
```

Bracket notation works too, and is useful when the property name is stored in a variable or contains characters that would not work with dot notation:

```javascript
console.log(student["major"]); // Computer Science

let propertyName = "age";
console.log(student[propertyName]); // 20
```

You can change a property's value the same way you would reassign a regular variable:

```javascript
student.age = 21;
console.log(student.age); // 21
```

You can also add a brand new property to an object simply by assigning it:

```javascript
student.graduationYear = 2027;
```

### Methods

A method is simply a function that is stored as a property of an object. Methods let an object describe things it can do, not just data it holds.

```javascript
let student = {
  name: "Alex",
  age: 20,
  introduce: function () {
    console.log("Hi, my name is " + this.name);
  }
};

student.introduce(); // Hi, my name is Alex
```

Notice the keyword `this` inside the method. Inside a method, `this` refers to the object the method belongs to. It is how the method reaches back into the object to use its own properties. 

`introduce` function can also be written in shorthand form:

```js
introduce () {
    console.log("Hi, my name is " + this.name);
}
```

You call a method the same way you call any function, except you use dot notation to reach it first.

### Exercise: Building With Functions and Objects

Using the HTML provided below, write JavaScript that creates a product object with the following requirements:

- Prompt the user for the product name, price, and quantity.
- Store the values as properties of the object.
- Create a `getTotalPrice()` method that returns the total cost (`price × quantity`).
- Create a `getReceipt()` method that returns a message showing the quantity, product name, and total cost.
Display the receipt using an `alert()`.

Here is the HTML:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Week 3</title>
</head>
<body>
    <h1>Welcome!</h1>
    <p>This page is running JavaScript</p>

  <script src="week_3.js"></script>
</body>
</html>
```

#### Solution
```js
let product = {
    name: prompt("Enter name of the product"),
    price: Number(prompt("Enter the price of the product")),
    quantity: Number(prompt("Enter the quantity of the product")),

    getTotalPrice()
    {
        return this.quantity * this.price;
    },
    getReceipt() {
        return `${this.quantity} items of ${this.name} cost: ${this.getTotalPrice()}`;
    }
};

alert(product.getReceipt());
```
#### Challenge:

Extend your product object by adding a discount feature. Add a discount property that stores a discount percentage entered by the user. Then:
- Create a getDiscountedPrice() method that calculates the total price after the discount.
- Update getReceipt() so that it displays the original total, discount percentage, and final discounted price.
- Use this to access the object's properties and methods.

**Example:**
If the user enters:
```
Product: Laptop
Price: 800
Quantity: 2
Discount: 10
```
The receipt could display:
```
2 items of Laptop
Original total: $1600
Discount: 10%
Final total: $1440
```
**Extra Challenge:** Add a method called getReceipt() that uses getDiscountedPrice() instead of calculating the discounted price directly.

## Events

### What Is an Event?

Every script you have written so far has run automatically, from top to bottom, the moment the page loaded. That works fine for scripts that do not need to wait on the user, but most real web pages need to respond to things the user does: clicking a button, typing in a field, moving the mouse.

An event is an action that happens in the browser, often triggered by the user, that JavaScript can detect and respond to. Programming this way is called event-driven programming. Instead of running straight through, the browser waits for something to happen, and then runs the code you have connected to that event.

### Common Event Types

| Event | Fires When |
| --- | --- |
| `click` | An element is clicked |
| `mouseover` | The mouse pointer moves onto an element |
| `mouseout` | The mouse pointer moves off of an element |
| `keydown` | A key is pressed down |
| `load` | The page finishes loading |
| `submit` | A form is submitted |


### Attaching an Event Handler

An event handler is the code that runs when a particular event occurs. The simplest way to attach one, and the method we will use this week, is an inline event attribute directly on the HTML element.

```html
<button onclick="showGreeting()">Say Hello</button>
```

The attribute name is the word `on` followed by the event type, so `click` becomes `onclick`. The value of the attribute is the name of a JavaScript function to run when that event fires. The function itself is defined in your external JS file, exactly the way you have been defining functions all week.

```javascript
function showGreeting() {
  alert("Hello! Thanks for clicking.");
}
```

Note: There is a more modern, more flexible way to attach event handlers called `addEventListener()`, which is paired with selecting an element using methods like `document.getElementById()`. That approach is part of scripting the DOM, which is covered formally in Week 4. For now, the inline attribute method is enough to practice event-driven thinking without getting ahead of material we have not covered yet.

### Using Functions and Objects Inside Event Handlers

The function connected to an event attribute is just a regular function, so everything you learned in Class 1 still applies. It can take parameters, return a value, use local variables, and work with objects.

```html
<button onclick="rollDice()">Roll the Dice</button>
```

```javascript
function rollDice() {
  let result = Math.floor(Math.random() * 6) + 1;
  alert("You rolled a " + result);
}
```

You can also have a click trigger a function that builds and uses an object, the same way you did in the Class 1 hands-on.

```html
<button onclick="createProfile()">Create Profile</button>
```

```javascript
function createProfile() {
  let user = {
    name: prompt("Enter your name:"),
    favoriteColor: prompt("Enter your favorite color:")
  };

  alert(user.name + "'s favorite color is " + user.favoriteColor);
}
```

### Exercise: Coffee Order Tracker
We will build a small ordering page for a coffee shop. Clicking a menu button adds that item to the customer's order, and a separate button displays a full order summary with a calculated total.

The HTML sets up a menu of buttons along with a button to view the order summary. Each menu button uses an inline onclick attribute that passes the item's name into a function.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Corner Cafe | Coffee Order Tracker</title>
    <style>
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
        .footer {
            margin-top: 25px;
            font-size: 13px;
            color: #998c85;
        }
    </style>
</head>
<body>
    <main class="container">
        <div class="logo">☕</div>
        <h1>Corner Cafe</h1>
        <p class="subtitle">
            Tap an item to add it to your order.
        </p>
        <section class="menu">
            <button onclick="addToOrder('coffee')">
                ☕ Coffee
                <span class="price">$3</span>
            </button>
            <button onclick="addToOrder('latte')">
                🥛 Latte
                <span class="price">$5</span>
            </button>
            <button onclick="addToOrder('muffin')">
                🧁 Muffin
                <span class="price">$4</span>
            </button>
        </section>
        <hr class="divider">
        <button class="summary-btn" onclick="showSummary()">
            View Order Summary
        </button>
        <p class="footer">
            Thank you for visiting Corner Cafe!
        </p>
    </main>
    <script src="week_3.js"></script>
</body>
</html>
```

#### Solution
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

function addToOrder(item) {
    order[item] = order[item] + 1;
    alert(item + " added to your order.");
}

function calculateTotal()
{
    let total = 0;
    for (let item in order) {
        total = total + order[item] * menu[item];
    }
    return total;
}

function showSummary()
{
    let receipt = "Order Summary\n";

    for (let item in order) {
        if (order[item] > 0) {
            receipt += order[item] + " x " + item + "\n";
        }
    }

    receipt += "Total: $" + calculateTotal();
    alert(receipt);
}
```

**Challenge:** Extend the Coffee Order Tracker to remove an item such that the remove function:
- Checks whether the customer has ordered the item.
- Checks if the quantity is greater than 0, decrease the quantity by 1.
- Checks if the quantity is already 0, display an appropriate message.

You might have to update the HTML file to add a remove button to each item.

## References

- Murach's JavaScript and jQuery (4th Edition)
  - Chapter 4: How to work with JavaScript objects, functions, and events
- W3Schools JavaScript Tutorials
  - [Functions](https://www.w3schools.com/js/js_functions.asp)
  - [Objects](https://www.w3schools.com/js/js_objects.asp)
  - [Events](https://www.w3schools.com/js/js_events.asp)
