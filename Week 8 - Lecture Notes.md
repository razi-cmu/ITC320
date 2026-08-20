# Week 8: Numbers, Dates, and Strings

So far, most of the data in your scripts has been stored in objects and read from the DOM as plain text. This week you will work with three built-in JavaScript types in more depth: numbers, dates, and strings. Each one comes with a set of built-in methods that make it much easier to validate input, calculate things, format output, and clean up messy text, all skills you will use in nearly every script from here forward.

## Working with Numbers

### Converting Strings to Numbers

Anything typed into an HTML input, even a number input, arrives in JavaScript as a string. Before you can do math with it, you often need to convert it to an actual number.

```javascript
const rawValue = "42";

const wholeNumber = parseInt(rawValue);   // 42
const decimalValue = parseFloat("3.14");  // 3.14
const strictNumber = Number("42");        // 42
```

`parseInt()` and `parseFloat()` read digits from the start of a string and stop as soon as they hit something that is not part of a number. `Number()` is stricter: it converts the whole string, and if any part of it is not a valid number, the result is `NaN` (Not a Number).

```javascript
parseInt("42px");   // 42, parseInt stops at "px"
Number("42px");     // NaN, the whole string must be numeric
```

### Checking for Valid Numbers with isNaN()

Because user input is unpredictable, it is good practice to check whether a conversion actually worked before using the result. This is the same defensive coding habit from Week 5, applied to numbers.

```javascript
const hikerInput = {
    name: "John",
};

const hikerCount = parseInt(hikerInput.value);

if (isNaN(hikerCount)) {
    console.log("Please enter a valid number");
}
else {
    console.log("Hiker Count: " + hikerCount);
            
}
```

### Rounding and Other Math Methods

The built-in `Math` object provides methods for rounding and comparing numbers.

```javascript
Math.round(4.5);   // 5, rounds to the nearest whole number
Math.floor(4.9);   // 4, always rounds down
Math.ceil(4.1);     // 5, always rounds up
Math.max(3, 7, 2);  // 7
Math.min(3, 7, 2);  // 2
```

### Formatting Numbers for Display

Raw numbers rarely look right on a page. Two methods handle most formatting needs, and they solve slightly different problems.

```javascript
const price = 19.5;

price.toFixed(2);          // "19.50", a string with exactly 2 decimal places
price.toLocaleString();    // "19.5", adds thousands separators for large numbers
```

`toFixed()` guarantees a fixed number of decimal places, which matters for prices. `toLocaleString()` is aware of number formatting conventions and is the better choice once numbers get large enough to need comma separators.

```javascript
const total = 1249.5;

total.toFixed(2);                                            // "1249.50"
total.toLocaleString();                                      // "1,249.5"
total.toLocaleString("en-US", { style: "currency", currency: "USD" });
// "$1,249.50"
```

Passing an options object to `toLocaleString()` can format a number as currency directly, combining both jobs into one method call. Either approach is fine for displaying a price; the important thing is being consistent within one script.

## Working with Dates

### Creating a Date Object

A `Date` object represents a single moment in time. With no argument, `new Date()` gives you the current date and time.

```javascript
const now = new Date();
const specificDay = new Date("2026-11-14");
```

### Getting Date Components

Once you have a `Date` object, you can pull individual pieces out of it.

```javascript
const tripDate = new Date(2026,11,14);

tripDate.getFullYear();
tripDate.getMonth(); 
tripDate.getDate();       
tripDate.getDay();   
```

### Formatting Dates for Display

You already saw `toLocaleDateString()` briefly back in Week 4. It is the easiest way to turn a `Date` object into something readable, without manually assembling the year, month, and day yourself.

```javascript
const tripDate = new Date("2026-11-14");

tripDate.toLocaleDateString();
// "11/14/2026" in most US browser locales

tripDate.toLocaleDateString("en-US", { weekday: "long", month: "long", day: "numeric", year: "numeric" });
// "Saturday, November 14, 2026"
```

### Simple Date Math

`Date` objects can be compared and subtracted, which is useful for anything involving a countdown or a duration. Behind the scenes, a `Date` stores a timestamp in milliseconds, and `getTime()` gives you access to that number directly.

```javascript
const today = new Date();
const tripDate = new Date("2026-11-14");

const millisecondsPerDay = 1000 * 60 * 60 * 24;
const daysUntilTrip = Math.ceil((tripDate.getTime() - today.getTime()) / millisecondsPerDay);
```

Subtracting two dates gives you a difference in milliseconds. Dividing by the number of milliseconds in a day converts that into days, and `Math.ceil()` rounds up so a trip that is a day and a half away still counts as 2 days out rather than 1.

**Checkpoint:** In the console, create a `Date` object for your own birthday this year and calculate how many days away it is using the pattern above.

## Working with Strings

### Reviewing Template Literals

Back in Week 2 you started using template literals (the backtick syntax) to combine text and variables. They are still the best way to build a sentence out of pieces of data.

```javascript
const name = "Jordan";
const hikerCount = 3;

console.log(`${name} is booking for ${hikerCount} hikers.`);
```

### Changing Case

```javascript
const city = "asheville";

city.toUpperCase();   // "ASHEVILLE"
city.toLowerCase();   // "asheville"
```

### Searching Within a String

```javascript
const email = "jordan@example.com";

email.includes("@");        // true
email.indexOf("@");         // 6, the position of the character, or -1 if not found
email.startsWith("jordan"); // true
email.endsWith(".com");     // true
```

### Extracting Parts of a String

```javascript
const fullName = "Jordan Vale";

fullName.slice(0, 6);   // "Jordan"
fullName.substring(7);  // "Vale"
```

`slice()` and `substring()` both pull out a portion of a string using start and end positions. `slice()` is generally the more flexible of the two, since it also accepts negative positions counted from the end of the string.

### Cleaning Up String Input

Text typed into a form field often has extra spaces or inconsistent capitalization. A few string methods, used together, can clean that up.

```javascript
function formatName(rawInput) {
  const trimmed = rawInput.trim();
  const firstLetter = trimmed.charAt(0).toUpperCase();
  const restOfName = trimmed.slice(1).toLowerCase();
  return firstLetter + restOfName;
}

formatName("  jORDAN  ");   // "Jordan"
```

`trim()` removes leading and trailing whitespace, `charAt()` grabs a single character at a given position, and combining `toUpperCase()` on the first letter with `toLowerCase()` on the rest produces consistent capitalization no matter how the user typed it.

**Checkpoint:** Save and refresh your test page, then log `formatName(" HIKER name ")` to the console and confirm it prints `"Hiker name"`.

## Exercise: Trailhead Adventure Tours Registration

Trailhead Adventure Tours needs a simple registration form for an upcoming guided hike. Given a finished `index.html` and `styles.css`, you will write `script.js` to clean up the visitor's name, validate the number of hikers, calculate how many days away the trip is, and calculate a total price with a group discount.

**Given: index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Trailhead Adventure Tours</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Trailhead Adventure Tours</h1>
    <p>Book your spot on the Saturday guided hike.</p>
  </header>

  <main>
    <form id="registration-form">
      <label for="hiker-name">Name</label>
      <input type="text" id="hiker-name" placeholder="Your full name">

      <label for="trip-date">Trip Date</label>
      <input type="date" id="trip-date" value="2026-11-14">

      <label for="hiker-count">Number of Hikers</label>
      <input type="number" id="hiker-count" min="1" value="1">

      <button type="submit">Register</button>
    </form>

    <section id="summary"></section>
  </main>
</body>
</html>
```

**Given: styles.css**

```css
body {
  font-family: Arial, sans-serif;
  max-width: 500px;
  margin: 40px auto;
  color: #222;
}

form {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

button {
  margin-top: 12px;
  padding: 10px;
  background-color: #2e7d32;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

#summary {
  margin-top: 20px;
  padding: 16px;
  background-color: #f1f1f1;
  border-radius: 6px;
  white-space: pre-line;
}
```

### Step 1: Select Elements and Handle the Form Submission

Start `script.js` by selecting the elements you will need and preventing the form's default page reload on submit.

```javascript
// script.js
const form = document.getElementById("registration-form");
const nameInput = document.getElementById("hiker-name");
const dateInput = document.getElementById("trip-date");
const hikerCountInput = document.getElementById("hiker-count");
const summary = document.getElementById("summary");

form.addEventListener("submit", function (event) {
  event.preventDefault();
  console.log("Form submitted");
});
```

Add the script tag to the HTML file. Refresh the page and click Register. Confirm the page does not reload and the console logs your message.

### Step 2: Clean Up the Name

Inside the submit handler, format the entered name using the trim and capitalize pattern from the Strings section above.

```javascript
form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);
  console.log(formattedName);
});

function formatName(rawInput) {
  const trimmed = rawInput.trim();
  const firstLetter = trimmed.charAt(0).toUpperCase();
  const restOfName = trimmed.slice(1).toLowerCase();
  return firstLetter + restOfName;
}
```

Save and refresh. Type `  jordan  ` into the name field and confirm the console logs `Jordan`.

### Step 3: Validate the Number of Hikers

Add a guard clause so the script does not continue if the hiker count is not a valid number, reusing the defensive coding habit from Week 5.

```javascript
form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);

  const hikerCount = parseInt(hikerCountInput.value);
  if (isNaN(hikerCount) || hikerCount < 1) {
    summary.textContent = "Please enter a valid number of hikers.";
    return;
  }

  console.log(formattedName, hikerCount);
});
```

Save and refresh. Clear the hiker count field entirely and submit. Confirm the summary section shows the error message instead of continuing.

### Step 4: Calculate Days Until the Trip

Add the date math from the Dates section to figure out how many days away the trip is.

```javascript
form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);

  const hikerCount = parseInt(hikerCountInput.value);
  if (isNaN(hikerCount) || hikerCount < 1) {
    summary.textContent = "Please enter a valid number of hikers.";
    return;
  }

  const tripDate = new Date(dateInput.value);
  const today = new Date();
  
  const millisecondsPerDay = 1000 * 60 * 60 * 24;
  const daysUntilTrip = Math.ceil((tripDate.getTime() - today.getTime()) / millisecondsPerDay);

  console.log(formattedName, hikerCount, daysUntilTrip);
});
```

Save and refresh. Confirm the console logs a reasonable number of days between today and the trip date.

### Step 5: Calculate the Total Price

Groups of 4 or more hikers get a 10% discount. Add the pricing logic and format the total as currency.

```javascript
const pricePerHiker = 75;

form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);

  const hikerCount = parseInt(hikerCountInput.value);
  if (isNaN(hikerCount) || hikerCount < 1) {
    summary.textContent = "Please enter a valid number of hikers.";
    return;
  }

  const tripDate = new Date(dateInput.value);
  const today = new Date();
  const millisecondsPerDay = 1000 * 60 * 60 * 24;
  const daysUntilTrip = Math.ceil((tripDate.getTime() - today.getTime()) / millisecondsPerDay);

  let total = pricePerHiker * hikerCount;
  if (hikerCount >= 4) {
    total = total * 0.9;
  }
  const formattedTotal = total.toLocaleString("en-US", { style: "currency", currency: "USD" });

  console.log(formattedName, hikerCount, daysUntilTrip, formattedTotal);
});
```

Save and refresh. Enter 4 or more hikers and confirm the logged total reflects the 10% discount.

### Step 6: Display the Summary

Finally, replace the console log with a message written to the page.

```javascript
form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);

  const hikerCount = parseInt(hikerCountInput.value);
  if (isNaN(hikerCount) || hikerCount < 1) {
    summary.textContent = "Please enter a valid number of hikers.";
    return;
  }

  const tripDate = new Date(dateInput.value);
  const today = new Date();
  const millisecondsPerDay = 1000 * 60 * 60 * 24;
  const daysUntilTrip = Math.ceil((tripDate.getTime() - today.getTime()) / millisecondsPerDay);

  let total = pricePerHiker * hikerCount;
  if (hikerCount >= 4) {
    total = total * 0.9;
  }
  const formattedTotal = total.toLocaleString("en-US", { style: "currency", currency: "USD" });
  const formattedDate = tripDate.toLocaleDateString("en-US", { weekday: "long", month: "long", day: "numeric", year: "numeric" });

  summary.textContent = `Thanks, ${formattedName}! You are booked for ${hikerCount} hiker(s) on ${formattedDate}, which is ${daysUntilTrip} day(s) away. Total: ${formattedTotal}`;
});
```

Save and refresh one more time. Fill out the form with a real name, a future date, and a hiker count of 4 or more, then submit and confirm the summary section shows a complete, correctly formatted message.

### Solution

```javascript
// script.js
const form = document.getElementById("registration-form");
const nameInput = document.getElementById("hiker-name");
const dateInput = document.getElementById("trip-date");
const hikerCountInput = document.getElementById("hiker-count");
const summary = document.getElementById("summary");

const pricePerHiker = 75;

form.addEventListener("submit", function (event) {
  event.preventDefault();

  const formattedName = formatName(nameInput.value);

  const hikerCount = parseInt(hikerCountInput.value);
  if (isNaN(hikerCount) || hikerCount < 1) {
    summary.textContent = "Please enter a valid number of hikers.";
    return;
  }

  const tripDate = new Date(dateInput.value);
  const today = new Date();
  const millisecondsPerDay = 1000 * 60 * 60 * 24;
  const daysUntilTrip = Math.ceil((tripDate.getTime() - today.getTime()) / millisecondsPerDay);

  let total = pricePerHiker * hikerCount;
  if (hikerCount >= 4) {
    total = total * 0.9;
  }
  const formattedTotal = total.toLocaleString("en-US", { style: "currency", currency: "USD" });
  const formattedDate = tripDate.toLocaleDateString("en-US", { weekday: "long", month: "long", day: "numeric", year: "numeric" });

  summary.textContent = `Thanks, ${formattedName}! You are booked for ${hikerCount} hiker(s) on ${formattedDate}, which is ${daysUntilTrip} day(s) away. Total: ${formattedTotal}`;
});

function formatName(rawInput) {
  const trimmed = rawInput.trim();
  const firstLetter = trimmed.charAt(0).toUpperCase();
  const restOfName = trimmed.slice(1).toLowerCase();
  return firstLetter + restOfName;
}
```

**Challenge:** Add a guard clause that checks whether the selected trip date has already passed (a negative `daysUntilTrip`). If it has, display a message asking the visitor to choose a future date instead of showing a booking summary. Then add an early-bird discount: if `daysUntilTrip` is greater than 30, apply an additional 5% off the total, on top of the group discount if both apply.

## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition, Mike Murach & Associates Inc., 2020. 
  - Chapter 8 (How to work with numbers, dates, and strings). 
- W3Schools Tutorials
  - [JavaScript Numbers](https://www.w3schools.com/js/js_numbers.asp)
  - [JavaScript Dates](https://www.w3schools.com/js/js_dates.asp)
  - [JavaScript Strings](https://www.w3schools.com/js/js_strings.asp)
