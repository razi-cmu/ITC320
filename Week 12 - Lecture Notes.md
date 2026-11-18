# Week 12: Effects, Animations, and Bootstrap

This week has two parts. The first part adds motion to a page with jQuery effects and animations. The second part introduces Bootstrap, a CSS framework that styles a page and adds ready-made widgets with very little code of your own. Both exercises continue the Northside Book Club Reading List from Week 11.

## Why Effects and Animations

So far, every change our scripts made to a page happened instantly. A card appeared, a message changed, a list was filtered, all in a single frame. Instant changes are easy to miss. A short effect shows the user what changed and where:

- A message that fades in and out confirms an action without blocking the page.
- A panel that slides open shows that new content came from that spot.
- A card that moves slightly shows which item the user just acted on.

Effects should support the content, not decorate it. Keep them short (under half a second for most), use them for feedback, and never make the user wait for an animation to finish before they can continue.

jQuery includes a set of effect methods in its full build. This is why Week 11 warned against the slim build: it removes effects as well as AJAX. Modern CSS can also animate elements, and the end of this section compares the two approaches.

## Demo Files Setup

Every theory example in this part runs on one demo page. Create a folder named `effects-demo` with the three files below, and open `index.html` with Live Server. Each later section says which buttons to click.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Effects Demo</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Effects Demo</h1>

  <p id="notice">Book club meets Tuesday at 7 PM.</p>

  <div class="toolbar">
    <button type="button" id="hide">Hide</button>
    <button type="button" id="show">Show</button>
    <button type="button" id="toggle">Toggle</button>
    <button type="button" id="fade-out">Fade Out</button>
    <button type="button" id="fade-in">Fade In</button>
    <button type="button" id="fade-to">Fade To 30%</button>
    <button type="button" id="change">Change Notice</button>
    <button type="button" id="flash">Flash</button>
  </div>

  <h2>FAQ</h2>
  <div class="faq">
    <h3 class="question">Do I need to buy the book?</h3>
    <p class="answer">No. The library holds two copies for members.</p>
    <h3 class="question">Can I bring a friend?</h3>
    <p class="answer">Yes. Guests are welcome at any meeting.</p>
  </div>

  <h2>Animate</h2>
  <div class="toolbar">
    <button type="button" id="move">Move Right</button>
    <button type="button" id="grow">Grow</button>
    <button type="button" id="reset">Reset</button>
  </div>
  <div id="box"></div>

  <h2>Hover</h2>
  <div id="hover-card">
    <p>Hover over this card</p>
    <p id="hover-panel">Next book: Glass Harbor</p>
  </div>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script src="demo.js"></script>
</body>
</html>
```

Given: `styles.css`

```css
body {
  font-family: Arial, sans-serif;
  max-width: 700px;
  margin: 20px auto;
  padding: 0 16px;
}

#notice {
  background: #fff3cd;
  border: 1px solid #e0c36b;
  padding: 12px;
}

.toolbar {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 12px 0;
}

.question {
  cursor: pointer;
  color: #3d5a80;
}

.answer {
  display: none;
}

#box {
  position: relative;
  width: 100px;
  height: 60px;
  background: #ee6c4d;
}

#hover-card {
  border: 1px solid #ccc;
  padding: 12px;
}

#hover-panel {
  display: none;
  background: #e0e7f1;
  padding: 8px;
}
```

Start `demo.js` with an empty ready function. Add each example inside it as you reach it.

```javascript
// demo.js

$(function () {

});
```

Checkpoint: The page shows the yellow notice, the buttons, two FAQ questions with no answers visible, an orange box, and the hover card. Clicking buttons does nothing yet.

## Showing and Hiding

You already know how to hide an element by adding a class. jQuery's `.hide()`, `.show()`, and `.toggle()` do the same job, and they can animate it.

```javascript
  $("#hide").on("click", () => $("#notice").hide("slow"));
  $("#show").on("click", () => $("#notice").show("slow"));
  $("#toggle").on("click", () => $("#notice").toggle(400));
```

Each effect method takes an optional duration:

| Duration | Meaning |
| --- | --- |
| no argument | `.hide()`, `.show()`, `.toggle()` happen instantly; other effects use 400 ms |
| `"fast"` | 200 ms |
| `"slow"` | 600 ms |
| a number | that many milliseconds, such as `400` |

When `.hide()` finishes, the element has `display: none`, so it no longer takes up space and the content below moves up. jQuery remembers the element's previous `display` value (such as `flex` or `block`), and `.show()` restores it.

`.toggle()` checks the current state each time: hidden elements are shown, visible elements are hidden.

Checkpoint: Click Hide, then Show, then Toggle twice. The notice shrinks away and grows back.

## Fading

Fading changes an element's opacity instead of its size.

```javascript
  $("#fade-out").on("click", () => $("#notice").fadeOut());
  $("#fade-in").on("click", () => $("#notice").fadeIn());
  $("#fade-to").on("click", () => $("#notice").fadeTo("fast", 0.3));
```

- `.fadeOut()` fades to transparent, then sets `display: none`.
- `.fadeIn()` makes the element visible and fades it up to full opacity.
- `.fadeToggle()` fades in or out depending on the current state.
- `.fadeTo(duration, opacity)` fades to a specific opacity between `0` and `1`. It requires both arguments, and the element stays visible and keeps its space, even at `0`.

Checkpoint: Click Fade Out and Fade In. Then click Fade To 30%. The notice stays in place but becomes pale. Click Fade In afterward: nothing happens, because `.fadeIn()` only works on hidden elements. Even Fade Out followed by Fade In brings it back at 30%, because jQuery restores the opacity the element had before it was hidden. Use `.fadeTo("fast", 1)` to return to full opacity, or reload the page.

## Sliding

Sliding changes an element's height, like a window blind. It is the usual effect for panels that open and close, such as an FAQ.

```javascript
  $(".question").on("click", function () {
    $(this).next(".answer").slideToggle();
  });
```

- `.slideDown()` shows a hidden element by growing its height from 0.
- `.slideUp()` shrinks it to 0, then hides it.
- `.slideToggle()` slides in whichever direction is needed.

This example reuses three ideas from Week 11: one listener attached to every `.question` by implicit iteration, a regular `function` so that `$(this)` is the clicked question, and traversal with `.next()` to reach the answer right after it.

The answers start hidden because `styles.css` gives `.answer` the rule `display: none`. Starting an element hidden in CSS and revealing it with an effect is a common pattern.

Checkpoint: Click each FAQ question. Its answer slides open. Click again and it slides closed. The other answer is not affected.

## Running Code After an Effect: Callbacks

Effects take time, but jQuery does not wait for them. The effect starts, and the next line of your code runs right away. Add this example and watch the Console:

```javascript
  $("#change").on("click", () => {
    $("#notice").fadeOut(() => {
      $("#notice").text("The meeting moved to Thursday.").fadeIn();
    });
    console.log("This runs first");
  });
```

The function passed to `.fadeOut()` is a callback. jQuery calls it once the fade has finished. That is why the text changes while the notice is invisible, instead of jumping halfway through the fade. The `console.log()` line runs immediately, before the callback, the same "later code first" order you saw with `async` functions in Week 10.

A callback goes after the duration if there is one: `.fadeOut("slow", callback)`.

Checkpoint: Click Change Notice. The notice fades out, comes back with the new text, and the Console shows "This runs first" as soon as you click.

## Chaining and the Effects Queue

You can chain effects. Instead of running at the same time, chained effects wait in a queue and run one after another. `.delay(ms)` adds a pause to the queue.

```javascript
  $("#flash").on("click", () => {
    $("#notice").fadeOut().fadeIn().delay(1000).fadeTo("slow", 0.5);
  });
```

The notice fades out, fades back in, waits one second, then fades to half opacity.

Only effects and `.delay()` wait in the queue. Other methods in the same chain, such as `.text()`, `.addClass()`, or `.css()`, run immediately:

```javascript
// The text changes at the start, not after the fade.
$("#notice").fadeOut().text("Changed").fadeIn();
```

When a change must happen after an effect, put it in a callback, as in the previous section.

Checkpoint: Click Flash. The notice blinks, pauses, and ends at half opacity. Reload the page to reset it.

## Custom Animations with .animate()

The built-in effects change display, opacity, or height. `.animate()` lets you animate other CSS properties. Pass an object of the property values to animate toward, then an optional duration.

```javascript
  $("#move").on("click", () => {
    $("#box").animate({ left: "+=60px" }, 500);
  });

  $("#grow").on("click", () => {
    $("#box").animate({ width: "200px", opacity: 0.5 }, "slow", "linear");
  });

  $("#reset").on("click", () => {
    $("#box").stop(true).animate({ left: "0px", width: "100px", opacity: 1 });
  });
```

Rules to remember:

- Only properties with numeric values can be animated: `width`, `height`, `left`, `top`, `opacity`, `margin`, `padding`, `fontSize`, and similar. Colors cannot be animated by jQuery on its own.
- Write property names in camelCase (`fontSize`, `marginLeft`), or put the CSS name in quotes (`"font-size"`).
- `"+=60px"` and `"-=60px"` are relative values: they add to or subtract from the current value. Each click on Move Right moves the box another 60 pixels.
- `left` and `top` only move an element that has a position other than the default. The box has `position: relative` in `styles.css`.
- The optional third argument is the easing, the speed pattern of the animation. jQuery has two built in: `"swing"` (the default, slower at the start and end) and `"linear"` (constant speed).

Several `.animate()` calls on the same element queue up, just like chained effects. The Reset button uses `.stop(true)`, explained next.

Checkpoint: Click Move Right three times quickly. The box moves 180 pixels in total, one step after another. Click Grow, then Reset.

## Stopping Animations

Because effects queue up, quick repeated events can build a long queue. Try this version of a hover panel first:

```javascript
  $("#hover-card").on("mouseenter", () => $("#hover-panel").slideDown());
  $("#hover-card").on("mouseleave", () => $("#hover-panel").slideUp());
```

Move the mouse in and out of the card quickly ten times, then stop. The panel keeps opening and closing long after the mouse has left, because every enter and leave added an effect to the queue.

`.stop()` ends the current animation. With `true` as its first argument, it also clears the queue, so only the newest effect runs. Replace the two lines with:

```javascript
  $("#hover-card").on("mouseenter", () => {
    $("#hover-panel").stop(true).slideDown();
  });

  $("#hover-card").on("mouseleave", () => {
    $("#hover-panel").stop(true).slideUp();
  });
```

| Method | What it does |
| --- | --- |
| `.stop()` | Stops the current animation where it is; queued effects still run |
| `.stop(true)` | Stops the current animation and clears the queue |
| `.stop(true, true)` | Clears the queue and jumps the current animation to its end state |
| `.finish()` | Jumps every queued animation to its end state |

Use `.stop(true)` before an effect whenever a user can trigger it faster than it finishes.

Checkpoint: Move the mouse in and out quickly again. The panel now follows the mouse and stops as soon as you stop.

## Effects, Accessibility, and CSS

Some users get dizzy or distracted by motion and turn on a "reduce motion" setting in their operating system. A page can check that setting with `window.matchMedia()`, and jQuery can turn every effect off with `$.fx.off`:

```javascript
$.fx.off = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
```

When `$.fx.off` is `true`, every effect jumps straight to its end state, and callbacks still run, so the page still works. You can test it in Chrome DevTools: open the Command Menu (Ctrl+Shift+P), type "reduced motion", and choose "Emulate CSS prefers-reduced-motion: reduce", then reload.

Modern CSS can also animate, with transitions. The script only changes a class, and CSS handles the motion:

```css
.panel {
  opacity: 1;
  transition: opacity 0.4s;
}

.panel.faded {
  opacity: 0;
}
```

```javascript
$(".panel").toggleClass("faded");
```

| | jQuery effects | CSS transitions |
| --- | --- | --- |
| Where the motion is defined | In JavaScript | In CSS |
| Hides with `display: none` at the end | Yes (`fadeOut`, `slideUp`) | No, needs extra code |
| Run code when finished | Callback argument | `transitionend` event |
| Performance | Good for small effects | Usually smoother; handled by the browser |
| Seen in | Many existing sites and plugins | Most new code |

You will read jQuery effects in existing code, and they are quick to write. For new projects, CSS transitions are usually preferred, and the class-toggling approach keeps style rules in CSS, as this course has done since Week 4.

## Exercise 1: Reading List with Effects

The Northside Book Club likes the jQuery Reading List from Week 11, but members say the page feels abrupt: filtering jumps, and nothing confirms that a vote was counted. Add effects that give clear, short feedback.

Make a copy of your Week 11 Exercise 1 folder. If your Week 11 version is incomplete, use the `books.js` below as your starting point. `index.html` and `styles.css` are the Week 11 files, with the changes listed under Step 1.

Given: `books.js` (Week 11 Exercise 1 Solution)

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

### Step 1: Add the Message Area and Details Layout

In `index.html`, add a message paragraph between the `#controls` section and the book list:

```html
    <p id="message"></p>
```

Given: additions to the end of `styles.css`

```css
#message {
  display: none;
  background: #e8f5e9;
  border-left: 4px solid #2e7d32;
  padding: 8px 12px;
}

.book-buttons {
  display: flex;
  gap: 8px;
}

.details {
  margin: 4px 0 0;
  font-size: 0.9em;
  color: #666;
}
```

The message starts hidden in CSS, the same pattern as the FAQ answers in the demo.

Checkpoint: The page looks and works exactly as it did in Week 11. The message area is invisible.

### Step 2: Fade the Cards In

In `renderBooks()`, hide each new card, add it to the list, then fade it in. A delay based on the card's position makes the cards appear one after another. `forEach()` passes the index as its second parameter.

```javascript
    list.forEach((book, index) => {
```

Replace the last line inside the loop with:

```javascript
      $("<li>").addClass("book").append(info, voteButton)
        .hide()
        .appendTo("#book-list")
        .delay(index * 100)
        .fadeIn();
```

Checkpoint: Reload the page. The six cards fade in from top to bottom.

### Step 3: Filter with a Fade

Fade the whole list out, then render the filtered books in the callback. Update the click handler in `addGenreButton()`:

```javascript
      .on("click", () => {
        $("#book-list").stop(true, true).fadeOut("fast", () => {
          if (label === "All") {
            renderBooks(books);
          } else {
            renderBooks(books.filter((book) => book.genre === label));
          }
        });
      })
```

After `.fadeOut()`, the list itself is hidden, so `renderBooks()` must show it again before the cards fade in. Update its first line:

```javascript
    $("#book-list").empty().show();
```

`.stop(true, true)` handles quick clicks: if a fade is still running, it jumps to its end so the latest click wins.

Checkpoint: Click Mystery. The list fades out and two cards fade in. Click Fantasy, Nonfiction, and All quickly. The page ends showing all six books, with no leftover cards.

### Step 4: Slide Book Details Open

Members want a shorter card. Show only the author on the card, and put the genre and pages in a hidden details paragraph that slides open with a Details button.

Inside the loop in `renderBooks()`, create the details paragraph first, hidden with `.hide()`:

```javascript
      const details = $("<p>")
        .addClass("details")
        .text(`${book.genre} | ${book.pages} pages`)
        .hide();
```

Change the `span` to show only `book.author`, and add `details` to `info`:

```javascript
      const info = $("<div>").addClass("book-info").append(
        $("<h3>").text(book.title),
        $("<span>").text(book.author),
        details
      );
```

Create the Details button. Its handler uses the `details` variable from the same loop pass, so each button opens its own card's details:

```javascript
      const detailsButton = $("<button>")
        .attr("type", "button")
        .text("Details")
        .on("click", () => details.slideToggle("fast"));
```

Put both buttons in a `.book-buttons` group and add it to the card in place of `voteButton`:

```javascript
      const buttons = $("<div>").addClass("book-buttons").append(detailsButton, voteButton);

      $("<li>").addClass("book").append(info, buttons)
```

Checkpoint: Each card shows its title and author with Details and Vote buttons. Clicking Details on Paper Moons slides open "Fantasy | 458 pages". Clicking again closes it.

### Step 5: Confirm Votes with a Message

Write `showMessage()`. It sets the text, stops any message animation already running, fades in, waits, and fades out, all in one chain:

```javascript
  function showMessage(text) {
    $("#message")
      .text(text)
      .stop(true, true)
      .fadeIn()
      .delay(1500)
      .fadeOut();
  }
```

Call it at the end of `addVote()`:

```javascript
    showMessage(`Vote counted for ${title}.`);
```

The text is set with `.text()`, not `.html()`, because it includes a book title (see Week 11).

Checkpoint: Vote for Field Notes. A green message appears, stays for about a second and a half, and fades out. Click Vote three times quickly. The message stays visible and fades out once, after the last click.

### Step 6: Nudge the Voted Card and Respect Reduced Motion

Give the voted card a small nudge with two queued `.animate()` calls: padding grows, then returns to the 16 pixels set in `styles.css`.

```javascript
  function nudge(card) {
    card.stop(true, true)
      .animate({ paddingLeft: "28px" }, 150)
      .animate({ paddingLeft: "16px" }, 150);
  }
```

The Vote button's handler needs the card, so store the card in a variable before creating the buttons, and use it at the end of the loop:

```javascript
      const card = $("<li>").addClass("book");
```

```javascript
      const voteButton = $("<button>")
        .attr("type", "button")
        .text("Vote")
        .on("click", () => {
          addVote(book.title);
          nudge(card);
        });
```

```javascript
      card.append(info, buttons)
        .hide()
        .appendTo("#book-list")
        .delay(index * 100)
        .fadeIn();
```

Finally, add the reduced-motion line as the first line inside the ready function:

```javascript
  $.fx.off = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
```

Checkpoint: Voting nudges the card to the right and back. In DevTools, emulate reduced motion and reload. The cards appear at once, filtering and Details work with no animation, and the vote message appears and disappears with no fade, after the same delay.

### Solution

```javascript
// books.js

$(function () {
  $.fx.off = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

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
    $("#book-list").empty().show();

    list.forEach((book, index) => {
      const details = $("<p>")
        .addClass("details")
        .text(`${book.genre} | ${book.pages} pages`)
        .hide();

      const info = $("<div>").addClass("book-info").append(
        $("<h3>").text(book.title),
        $("<span>").text(book.author),
        details
      );

      const card = $("<li>").addClass("book");

      const detailsButton = $("<button>")
        .attr("type", "button")
        .text("Details")
        .on("click", () => details.slideToggle("fast"));

      const voteButton = $("<button>")
        .attr("type", "button")
        .text("Vote")
        .on("click", () => {
          addVote(book.title);
          nudge(card);
        });

      const buttons = $("<div>").addClass("book-buttons").append(detailsButton, voteButton);

      card.append(info, buttons)
        .hide()
        .appendTo("#book-list")
        .delay(index * 100)
        .fadeIn();
    });
  }

  function addGenreButton(label) {
    $("<button>")
      .attr("type", "button")
      .text(label)
      .on("click", () => {
        $("#book-list").stop(true, true).fadeOut("fast", () => {
          if (label === "All") {
            renderBooks(books);
          } else {
            renderBooks(books.filter((book) => book.genre === label));
          }
        });
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
    showMessage(`Vote counted for ${title}.`);
  }

  function showVotes() {
    $("#vote-list").empty();

    votes.forEach((count, title) => {
      $("<li>").text(`${title}: ${count}`).appendTo("#vote-list");
    });
  }

  function showMessage(text) {
    $("#message")
      .text(text)
      .stop(true, true)
      .fadeIn()
      .delay(1500)
      .fadeOut();
  }

  function nudge(card) {
    card.stop(true, true)
      .animate({ paddingLeft: "28px" }, 150)
      .animate({ paddingLeft: "16px" }, 150);
  }

  const genres = new Set(books.map((book) => book.genre));

  addGenreButton("All");
  genres.forEach((genre) => addGenreButton(genre));

  renderBooks(books);
});
```

**Challenge:** Add a Hide Votes button that slides the vote list closed and open. The button's text should change to "Show Votes" when the list is hidden and back to "Hide Votes" when it is shown, and the text should change only after the slide finishes.

Add the button to `index.html`, right after the Votes heading:

```html
      <button type="button" id="toggle-votes">Hide Votes</button>
```

Add this code inside the ready function, after `renderBooks(books);`. The `:visible` selector with `.is()` checks whether the list is currently showing, and the check is inside the callback so it runs after the slide has finished.

```javascript
  $("#toggle-votes").on("click", () => {
    $("#vote-list").slideToggle(() => {
      if ($("#vote-list").is(":visible")) {
        $("#toggle-votes").text("Hide Votes");
      } else {
        $("#toggle-votes").text("Show Votes");
      }
    });
  });
```

Try moving the `if` statement outside the callback, just after `.slideToggle()`. The text is now wrong, because the check runs before the slide has changed anything.

## What Bootstrap Is

Every page in this course so far has been styled by hand in `styles.css`. Bootstrap is a free CSS framework: a large, ready-made stylesheet of classes for layout, spacing, colors, buttons, cards, and more. You style an element by adding Bootstrap's classes to its HTML, and you write much less CSS of your own.

Bootstrap also includes JavaScript widgets, sometimes called plugins or components: modals (dialog boxes), dropdowns, tooltips, collapsible panels, and carousels. Many work with no JavaScript of your own at all, driven by attributes in the HTML.

This course uses Bootstrap 5.3.8, the current version. Two facts matter when reading existing code:

- Bootstrap 5 does not need jQuery. Bootstrap 3 and 4 did, so older sites often load jQuery and Bootstrap together, and their code may open widgets with jQuery calls such as `$("#myModal").modal("show")`. In Bootstrap 5, that becomes plain JavaScript, shown later in this part.
- jQuery plugins are a separate idea. A jQuery plugin adds new methods to jQuery selections. jQuery UI, for example, adds `$("#date").datepicker()` and color animation. You will see plugins in older projects; you do not need them for Bootstrap 5.

You can still use jQuery and Bootstrap on the same page, as Exercise 2 does: Bootstrap handles styling and widgets, and jQuery handles your page logic.

In this part, you start with a plain page and add Bootstrap one piece at a time, so you can see exactly what each class does.

## Bootstrap Demo Setup

Create a folder named `bootstrap-demo` with the two files below and open `index.html` with Live Server. The page has no Bootstrap yet and no `styles.css`, so it shows the browser's default styles.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bootstrap Demo</title>
</head>
<body>
  <main>
    <h1>Club Events</h1>
    <p>Sign-up for spring events opens next week.</p>

    <button type="button">Join the Club</button>
    <button type="button">View Calendar</button>

    <div>
      <h2>Author Night</h2>
      <p>Meet a local author and ask questions.</p>
      <button type="button">Reserve a Seat</button>
    </div>
  </main>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script src="demo.js"></script>
</body>
</html>
```

Given: `demo.js`

```javascript
// demo.js

$(function () {

});
```

The viewport `meta` tag, which every page in this course already has, matters more than usual here: Bootstrap's layout depends on it for phones.

Checkpoint: The page shows a large serif heading, plain gray buttons, and the event text, all in the browser's default style.

## Adding Bootstrap's CSS

Add Bootstrap's stylesheet in the `head`, after the `title`. Add nothing else yet.

```html
  <link rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
    integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB"
    crossorigin="anonymous">
```

Reload. Without a single class, the page already looks different: a modern sans-serif font, adjusted heading sizes and margins, and the text now touches the left edge of the window. This is Bootstrap's reset, called Reboot. It replaces the different default styles of each browser with one consistent starting point. The body's margin is removed on purpose; Bootstrap expects you to add spacing with its own classes, which you will do shortly.

The `integrity` attribute is a fingerprint of the exact file. If the file the CDN sends does not match it, the browser refuses to use it, which protects the page if the CDN is ever compromised. Copy the value exactly; a single wrong character blocks the file. Week 14 returns to this when we revisit security.

If your page later has its own `styles.css`, link it after Bootstrap's stylesheet, so your rules can override Bootstrap's when needed.

Checkpoint: The font and spacing change, and the text sits against the left edge. The buttons still look almost the same.

## Buttons

Bootstrap styles an element only when you add its classes. Add `btn` to the Join the Club button:

```html
    <button type="button" class="btn">Join the Club</button>
```

The button gets padding and rounded corners, but it loses its background and now looks like plain text. `btn` is the base class: it sets the shape that all Bootstrap buttons share. A second class sets the color:

```html
    <button type="button" class="btn btn-primary">Join the Club</button>
```

Now style the second button with an outline version of a different color, and make the first one larger:

```html
    <button type="button" class="btn btn-primary btn-lg">Join the Club</button>
    <button type="button" class="btn btn-outline-secondary">View Calendar</button>
```

This is the pattern for most Bootstrap components: one base class, plus modifier classes that change the color, style, or size.

| Class | What it does |
| --- | --- |
| `btn` | Base button shape (required) |
| `btn-primary`, `btn-success`, `btn-danger`, ... | Filled button in that color |
| `btn-outline-primary`, `btn-outline-secondary`, ... | Outlined button, filled on hover |
| `btn-sm`, `btn-lg` | Smaller or larger button |

Bootstrap's color names are shared across many classes: `primary` (blue), `secondary` (gray), `success` (green), `warning` (yellow), `danger` (red), `info` (light blue), `light`, and `dark`.

Checkpoint: Join the Club is a large blue button. View Calendar is gray text with a gray border that fills when you hover over it.

## Spacing and Text Utilities

Utility classes each do one small job, such as adding margin or centering text. Fix the text touching the edge first. Add `container` and `py-4` to `main`:

```html
  <main class="container py-4">
```

`container` centers the content and gives it a maximum width with padding on the sides. `py-4` adds padding to the top and bottom. Now center the heading with some space below it, and make the paragraph gray:

```html
    <h1 class="text-center mb-4">Club Events</h1>
    <p class="text-body-secondary">Sign-up for spring events opens next week.</p>
```

Spacing classes follow one pattern: the property, an optional side, and a size from `0` to `5`.

| Class | What it does |
| --- | --- |
| `m-3`, `p-3` | Margin (`m`) or padding (`p`) on all sides |
| `mt-4`, `mb-3`, `ms-2`, `me-2` | One side: `t` top, `b` bottom, `s` start (left), `e` end (right) |
| `px-2`, `py-4` | Both sides: `x` left and right, `y` top and bottom |
| `text-center` | Centered text |
| `text-body-secondary` | Gray, secondary text |
| `bg-primary`, `bg-dark` | Background color from Bootstrap's palette |
| `d-flex`, `flex-wrap`, `gap-2` | Flexbox layout with spacing, the same layout you wrote by hand for `#controls` |
| `d-none` | Hides the element (`display: none`) |

Checkpoint: The content now has space on both sides and is centered in wide windows. In DevTools Elements, change `mb-4` on the heading to `mb-0`, then to `mb-5`, and watch the space below it change. Set it back to `mb-4`.

## Cards

A card is a bordered box for one item, like each book in the Reading List. Build one in layers from the Author Night `div`. First, add `card`:

```html
    <div class="card">
```

The `div` gets a border and rounded corners, but the text touches the border. The padding comes from a second element: wrap the card's contents in a `div` with the class `card-body`.

```html
    <div class="card">
      <div class="card-body">
        <h2>Author Night</h2>
        <p>Meet a local author and ask questions.</p>
        <button type="button">Reserve a Seat</button>
      </div>
    </div>
```

Finally, style the parts inside the card:

```html
    <div class="card">
      <div class="card-body">
        <h2 class="card-title h5">Author Night</h2>
        <p class="card-text">Meet a local author and ask questions.</p>
        <button type="button" class="btn btn-primary">Reserve a Seat</button>
      </div>
    </div>
```

- `card-title` and `card-text` set the spacing for a title and its text.
- `h5` on an `h2` makes the heading look the size of an `h5` while keeping the correct heading order for screen readers.
- The button inside uses the same button classes as before.

Checkpoint: Author Night is a white box with a border, a smaller title, padding on all sides, and a blue button. It stretches across the full width of the container.

## The Grid System

The card is too wide on its own. Bootstrap's grid places items side by side in rows and columns:

- A `row` holds columns side by side. `g-3` sets the gap between them.
- Each row is 12 columns wide. A column class says how many of the 12 an element uses.

Replace the card with a row of three cards. Each card sits inside a column `div`, and the first card is the one you just built:

```html
    <div class="row g-3 mt-2">
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Author Night</h2>
            <p class="card-text">Meet a local author and ask questions.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Book Swap</h2>
            <p class="card-text">Bring one book, take one home.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Reading Marathon</h2>
            <p class="card-text">Read aloud in shifts for a full day, with snacks and short breaks.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
    </div>
```

`h-100` (height 100%) makes every card fill its column, so the three cards stay the same height even though the Reading Marathon text is longer.

Column classes can include a breakpoint, the screen width at which the rule starts. Below a column's breakpoint, the column takes the full row.

| Breakpoint | Starts at | Example |
| --- | --- | --- |
| none | 0 px (all screens) | `col-6`: 6 of 12 columns (half width) everywhere |
| `sm` | 576 px | `col-sm-6` |
| `md` | 768 px | `col-md-6`: half width on tablets and up |
| `lg` | 992 px | `col-lg-4`: one third on laptops and up |
| `xl` | 1200 px | `col-xl-3` |

The cards use `col-md-6 col-lg-4`: one card per row on phones, two per row on tablets, and three per row on larger screens. This is the same responsive idea as CSS media queries, without writing any.

Checkpoint: Open the DevTools device toolbar (Week 9) and change the width. The cards change from three per row, to two, to one.

## Widgets That Need JavaScript: Modals

Everything so far has been CSS only. Some components need Bootstrap's JavaScript as well. A modal is a dialog box that opens over the page. Add a Club Rules button below the row of cards:

```html
    <button type="button" class="btn btn-dark mt-4" data-bs-toggle="modal" data-bs-target="#rules-modal">
      Club Rules
    </button>
```

Then add the modal itself after the closing `</main>` tag:

```html
  <div class="modal fade" id="rules-modal" tabindex="-1" aria-labelledby="rules-title" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h2 class="modal-title h5" id="rules-title">Club Rules</h2>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <p>Finish the book if you can. Be kind about the ending. Bring snacks to share.</p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
```

Reload and click Club Rules. Nothing happens, and the Console shows no error. The modal's HTML and CSS are on the page (the CSS hides it), but nothing is listening for the click yet.

Add Bootstrap's JavaScript bundle before your own script, after the jQuery tag:

```html
  <script
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-FKyoEForCGlyvwx9Hj09JcYn3nv7wiPVlz7YYwJrWVcXK/BmnVDxM+D2scQbITxI"
    crossorigin="anonymous"></script>
```

The bundle includes Popper, a small library that Bootstrap's dropdowns and tooltips use for positioning. You can check that it loaded in the Console:

```javascript
typeof bootstrap
```

It returns `"object"` when Bootstrap's JavaScript has loaded, and `"undefined"` if the script tag is wrong.

Click Club Rules again. The modal now opens. No code in `demo.js` did this; three attributes did:

- `data-bs-toggle="modal"` tells Bootstrap's script that this button controls a modal.
- `data-bs-target="#rules-modal"` names which modal, by selector.
- `data-bs-dismiss="modal"` makes a button inside the modal close it.

These are the same `data-*` attributes you read with `.data()` in Week 11. Bootstrap's script reads them when the page loads and attaches the listeners for you. The `fade` class gives the modal Bootstrap's own CSS transition, and the `aria-` attributes describe the dialog to screen readers.

To open a widget from your own code, use Bootstrap's JavaScript object. Add a second button next to Club Rules:

```html
    <button type="button" class="btn btn-outline-dark mt-4" id="open-js">Rules from JavaScript</button>
```

Then add this inside the ready function in `demo.js`:

```javascript
  $("#open-js").on("click", () => {
    bootstrap.Modal.getOrCreateInstance("#rules-modal").show();
  });
```

`getOrCreateInstance()` returns the modal object for that element, creating it the first time, and `.show()` opens it. In Bootstrap 4 code, the same line is written with jQuery as `$("#rules-modal").modal("show")`.

Checkpoint: Club Rules and Rules from JavaScript both open the dialog over a dimmed page. Close it with the X, the Close button, the Escape key, or a click outside it.

## Complete Demo Page

Your finished `index.html` should match this file. Compare against it if a section did not work as described.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bootstrap Demo</title>
  <link rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
    integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB"
    crossorigin="anonymous">
</head>
<body>
  <main class="container py-4">
    <h1 class="text-center mb-4">Club Events</h1>
    <p class="text-body-secondary">Sign-up for spring events opens next week.</p>

    <button type="button" class="btn btn-primary btn-lg">Join the Club</button>
    <button type="button" class="btn btn-outline-secondary">View Calendar</button>

    <div class="row g-3 mt-2">
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Author Night</h2>
            <p class="card-text">Meet a local author and ask questions.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Book Swap</h2>
            <p class="card-text">Bring one book, take one home.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
      <div class="col-md-6 col-lg-4">
        <div class="card h-100">
          <div class="card-body">
            <h2 class="card-title h5">Reading Marathon</h2>
            <p class="card-text">Read aloud in shifts for a full day, with snacks and short breaks.</p>
            <button type="button" class="btn btn-primary">Reserve a Seat</button>
          </div>
        </div>
      </div>
    </div>

    <button type="button" class="btn btn-dark mt-4" data-bs-toggle="modal" data-bs-target="#rules-modal">
      Club Rules
    </button>
    <button type="button" class="btn btn-outline-dark mt-4" id="open-js">Rules from JavaScript</button>
  </main>

  <div class="modal fade" id="rules-modal" tabindex="-1" aria-labelledby="rules-title" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h2 class="modal-title h5" id="rules-title">Club Rules</h2>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <p>Finish the book if you can. Be kind about the ending. Bring snacks to share.</p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-FKyoEForCGlyvwx9Hj09JcYn3nv7wiPVlz7YYwJrWVcXK/BmnVDxM+D2scQbITxI"
    crossorigin="anonymous"></script>
  <script src="demo.js"></script>
</body>
</html>
```

The Bootstrap documentation shows the HTML for every component. A common workflow is to copy an example from the docs and change its content, as Exercise 2 does for three new components.

## Exercise 2: Reading List with Bootstrap

The club wants the Reading List to look modern and work well on phones, without a long stylesheet to maintain. Restyle your Exercise 1 page with Bootstrap. Along the way, you will use the buttons, cards, and grid from the demo, and add three new components: a navbar, an alert, and a progress bar. The page should keep everything it did at the end of Exercise 1, effects included.

Make a copy of your Exercise 1 folder. Replace `index.html` and `styles.css` with the files below. `index.html` already loads Bootstrap's CSS and JavaScript and uses a few of the utility classes from the demo. The new `styles.css` is only two rules, because Bootstrap replaces the rest. The Challenge's Hide Votes button from Exercise 1 is not part of this page.

Given: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Northside Book Club</title>
  <link rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
    integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB"
    crossorigin="anonymous">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Northside Book Club</h1>
  </header>

  <main class="container py-4">
    <p class="text-body-secondary">This season's reading list</p>

    <div id="genre-buttons" class="d-flex flex-wrap gap-2 mb-3"></div>

    <p id="message"></p>

    <div id="book-list" class="row g-3"></div>

    <section id="votes" class="mt-4">
      <h2 class="h4">Votes</h2>
      <div id="vote-list" class="d-flex flex-wrap gap-2"></div>
    </section>
  </main>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-FKyoEForCGlyvwx9Hj09JcYn3nv7wiPVlz7YYwJrWVcXK/BmnVDxM+D2scQbITxI"
    crossorigin="anonymous"></script>
  <script src="books.js"></script>
</body>
</html>
```

Given: `styles.css`

```css
body {
  background: #f6f4ef;
}

#message {
  display: none;
}
```

Notice the changes from Exercise 1: the book list is now a `div` with the `row` class instead of a `ul`, so its children can be grid columns, and the vote list is a flex `div`. Step 4 explains why the message is hidden in `styles.css`.

### Step 1: Replace the Header with a Navbar

A navbar is the bar across the top of a site that holds its name and, often, its main links. Replace the `header` element with:

```html
  <nav class="navbar bg-primary" data-bs-theme="dark">
    <div class="container">
      <span class="navbar-brand">Northside Book Club</span>
    </div>
  </nav>
```

- `navbar` is the base class, and `navbar-brand` styles the site name.
- `bg-primary` is the same background utility from the demo.
- `data-bs-theme="dark"` tells Bootstrap the background is dark, so it uses light text.
- The `container` inside lines the site name up with the page content below it.

Checkpoint: A blue bar with the club name in white runs across the top. `typeof bootstrap` in the Console shows `"object"`. The books still appear, but with no card styling and a messy layout, because `books.js` still builds the Exercise 1 HTML, whose `.book` rules are no longer in `styles.css`.

### Step 2: Style the Genre Buttons

In `addGenreButton()`, add Bootstrap button classes after `.attr("type", "button")`:

```javascript
      .addClass("btn btn-outline-primary btn-sm")
```

Checkpoint: The genre buttons are small blue outline buttons, and filtering still fades the list.

### Step 3: Build Bootstrap Cards in the Grid

In `renderBooks()`, each book becomes a grid column holding a card, the same structure as the demo's event cards. Update the loop:

- Add `small mb-2` to the details paragraph's classes: `.addClass("details small mb-2")`. `small` makes the text smaller.
- The card is now a `div` with Bootstrap card classes. The orange left border from Week 10 becomes `border-start border-4 border-warning`: a border on the start (left) side, 4 pixels wide, in the warning color.
- Add `btn btn-outline-secondary btn-sm` to the Details button and `btn btn-primary btn-sm` to the Vote button.
- Replace `info` and the `.book-buttons` group with a `card-body` that holds the title, author, details, and a `d-flex gap-2` group for the two buttons.
- The column, not the card, is hidden, added to the list, and faded in.

```javascript
      const card = $("<div>").addClass("card h-100 border-start border-4 border-warning");
```

```javascript
      const body = $("<div>").addClass("card-body").append(
        $("<h3>").addClass("card-title h5").text(book.title),
        $("<p>").addClass("card-text text-body-secondary mb-1").text(book.author),
        details,
        $("<div>").addClass("d-flex gap-2").append(detailsButton, voteButton)
      );

      $("<div>")
        .addClass("col-md-6 col-lg-4")
        .append(card.append(body))
        .hide()
        .appendTo("#book-list")
        .delay(index * 100)
        .fadeIn();
```

The Bootstrap card's padding is on `card-body`, not on the card, so update `nudge()` to move the card with its margin instead:

```javascript
  function nudge(card) {
    card.stop(true, true)
      .animate({ marginLeft: "8px" }, 150)
      .animate({ marginLeft: "0px" }, 150);
  }
```

Checkpoint: The books appear as cards, three per row on a wide window, two on a tablet width, and one on a phone width (use the device toolbar). Details still slides open, and Vote still nudges the card.

### Step 4: Turn the Message into an Alert

An alert is a colored message box. Like buttons, it takes a base class and a color class. In `index.html`, add these classes to the message paragraph:

```html
    <p id="message" class="alert alert-success"></p>
```

Other colors work the same way: `alert-info`, `alert-warning`, `alert-danger`.

The message stays hidden until a vote, because of the `#message { display: none; }` rule in `styles.css`. Bootstrap also has a `d-none` class that hides an element, so why not use it? Try it:

1. Add `d-none` to the paragraph's classes.
2. Delete the `#message` rule from `styles.css`.
3. Reload and vote for a book.

No message appears, and the Console shows no error. `d-none` is defined as `display: none !important`, and `!important` beats the inline `display` value that `.fadeIn()` and `.show()` set. Undo both changes.

The rule: if jQuery will show an element, hide it with your own CSS rule or with `.hide()` in your script, not with `d-none`. If an element must keep `d-none`, show it by removing the class: `.removeClass("d-none")`.

Checkpoint: Vote for Field Notes. A green alert box fades in with "Vote counted for Field Notes." and fades out.

### Step 5: Show Votes as Badges

A badge is a small label. In `showVotes()`, create a pill-shaped badge for each vote instead of an `li`:

```javascript
    votes.forEach((count, title) => {
      $("<span>")
        .addClass("badge rounded-pill text-bg-primary")
        .text(`${title}: ${count}`)
        .appendTo("#vote-list");
    });
```

- `badge` is the base class.
- `rounded-pill` gives it fully rounded ends.
- `text-bg-primary` sets a blue background with a text color that is readable on it.

Checkpoint: Vote for Glass Harbor twice and Small Habits once. Two blue pills appear under Votes: "Glass Harbor: 2" and "Small Habits: 1".

### Step 6: Add a Vote Goal Progress Bar

The club wants 10 votes before the next meeting. Show progress toward that goal with a progress bar. In `index.html`, add this inside the `#votes` section, right after the Votes heading:

```html
      <p class="mb-1">Goal: 10 votes</p>
      <div class="progress mb-3" role="progressbar" aria-label="Votes toward goal"
        aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">
        <div id="goal-bar" class="progress-bar" style="width: 0%">0 / 10</div>
      </div>
```

- `progress` is the gray track, and `progress-bar` is the colored bar inside it.
- The bar's length is its `width`, as a percentage of the track. Bootstrap's documentation sets it with a `style` attribute because it changes with the data, not with the design.
- `role="progressbar"` and the `aria-value...` attributes tell screen readers what the bar means and its current value.

In `books.js`, add a goal constant and a vote counter below the `votes` Map:

```javascript
  const VOTE_GOAL = 10;
  let totalVotes = 0;
```

Write `updateGoal()`. It works out the percentage, never more than 100, and updates the bar's width, its text, and the `aria-valuenow` value:

```javascript
  function updateGoal() {
    const percent = Math.min((totalVotes / VOTE_GOAL) * 100, 100);

    $("#goal-bar")
      .css("width", `${percent}%`)
      .text(`${totalVotes} / ${VOTE_GOAL}`)
      .toggleClass("bg-success", totalVotes >= VOTE_GOAL);

    $(".progress").attr("aria-valuenow", totalVotes);
  }
```

`toggleClass()` with a second argument adds the class when the argument is `true` and removes it when it is `false`, so the bar turns green once the goal is reached.

Notice that `updateGoal()` sets the width with `.css()`, not `.animate()`, yet the bar still grows smoothly. Bootstrap's stylesheet gives `progress-bar` a CSS transition on `width`, the CSS approach compared with jQuery effects in Class 1. Bootstrap also turns that transition off when the user has asked for reduced motion.

Finally, count each vote in `addVote()`. Add these two lines before `showVotes();`:

```javascript
    totalVotes++;
    updateGoal();
```

Checkpoint: Each vote grows the bar by one tenth and updates its text, for example "3 / 10". After the tenth vote, the bar is full and green. More votes keep it at full width while the text keeps counting.

### Solution

`index.html` after Steps 1, 4, and 6:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Northside Book Club</title>
  <link rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
    integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB"
    crossorigin="anonymous">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <nav class="navbar bg-primary" data-bs-theme="dark">
    <div class="container">
      <span class="navbar-brand">Northside Book Club</span>
    </div>
  </nav>

  <main class="container py-4">
    <p class="text-body-secondary">This season's reading list</p>

    <div id="genre-buttons" class="d-flex flex-wrap gap-2 mb-3"></div>

    <p id="message" class="alert alert-success"></p>

    <div id="book-list" class="row g-3"></div>

    <section id="votes" class="mt-4">
      <h2 class="h4">Votes</h2>
      <p class="mb-1">Goal: 10 votes</p>
      <div class="progress mb-3" role="progressbar" aria-label="Votes toward goal"
        aria-valuenow="0" aria-valuemin="0" aria-valuemax="10">
        <div id="goal-bar" class="progress-bar" style="width: 0%">0 / 10</div>
      </div>
      <div id="vote-list" class="d-flex flex-wrap gap-2"></div>
    </section>
  </main>

  <script src="https://code.jquery.com/jquery-4.0.0.min.js"></script>
  <script
    src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-FKyoEForCGlyvwx9Hj09JcYn3nv7wiPVlz7YYwJrWVcXK/BmnVDxM+D2scQbITxI"
    crossorigin="anonymous"></script>
  <script src="books.js"></script>
</body>
</html>
```

```javascript
// books.js

$(function () {
  $.fx.off = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

  const books = [
    { title: "The Silent Orchard", author: "Lena Hart", genre: "Mystery", pages: 312 },
    { title: "Paper Moons", author: "Omar Reyes", genre: "Fantasy", pages: 458 },
    { title: "Field Notes", author: "Priya Nand", genre: "Nonfiction", pages: 224 },
    { title: "The Last Ferry", author: "Sam Okafor", genre: "Mystery", pages: 276 },
    { title: "Glass Harbor", author: "Ines Duarte", genre: "Fantasy", pages: 390 },
    { title: "Small Habits", author: "Theo Lund", genre: "Nonfiction", pages: 198 }
  ];

  const votes = new Map();
  const VOTE_GOAL = 10;
  let totalVotes = 0;

  function renderBooks(list) {
    $("#book-list").empty().show();

    list.forEach((book, index) => {
      const details = $("<p>")
        .addClass("details small mb-2")
        .text(`${book.genre} | ${book.pages} pages`)
        .hide();

      const card = $("<div>").addClass("card h-100 border-start border-4 border-warning");

      const detailsButton = $("<button>")
        .attr("type", "button")
        .addClass("btn btn-outline-secondary btn-sm")
        .text("Details")
        .on("click", () => details.slideToggle("fast"));

      const voteButton = $("<button>")
        .attr("type", "button")
        .addClass("btn btn-primary btn-sm")
        .text("Vote")
        .on("click", () => {
          addVote(book.title);
          nudge(card);
        });

      const body = $("<div>").addClass("card-body").append(
        $("<h3>").addClass("card-title h5").text(book.title),
        $("<p>").addClass("card-text text-body-secondary mb-1").text(book.author),
        details,
        $("<div>").addClass("d-flex gap-2").append(detailsButton, voteButton)
      );

      $("<div>")
        .addClass("col-md-6 col-lg-4")
        .append(card.append(body))
        .hide()
        .appendTo("#book-list")
        .delay(index * 100)
        .fadeIn();
    });
  }

  function addGenreButton(label) {
    $("<button>")
      .attr("type", "button")
      .addClass("btn btn-outline-primary btn-sm")
      .text(label)
      .on("click", () => {
        $("#book-list").stop(true, true).fadeOut("fast", () => {
          if (label === "All") {
            renderBooks(books);
          } else {
            renderBooks(books.filter((book) => book.genre === label));
          }
        });
      })
      .appendTo("#genre-buttons");
  }

  function addVote(title) {
    if (votes.has(title)) {
      votes.set(title, votes.get(title) + 1);
    } else {
      votes.set(title, 1);
    }

    totalVotes++;
    updateGoal();
    showVotes();
    showMessage(`Vote counted for ${title}.`);
  }

  function updateGoal() {
    const percent = Math.min((totalVotes / VOTE_GOAL) * 100, 100);

    $("#goal-bar")
      .css("width", `${percent}%`)
      .text(`${totalVotes} / ${VOTE_GOAL}`)
      .toggleClass("bg-success", totalVotes >= VOTE_GOAL);

    $(".progress").attr("aria-valuenow", totalVotes);
  }

  function showVotes() {
    $("#vote-list").empty();

    votes.forEach((count, title) => {
      $("<span>")
        .addClass("badge rounded-pill text-bg-primary")
        .text(`${title}: ${count}`)
        .appendTo("#vote-list");
    });
  }

  function showMessage(text) {
    $("#message")
      .text(text)
      .stop(true, true)
      .fadeIn()
      .delay(1500)
      .fadeOut();
  }

  function nudge(card) {
    card.stop(true, true)
      .animate({ marginLeft: "8px" }, 150)
      .animate({ marginLeft: "0px" }, 150);
  }

  const genres = new Set(books.map((book) => book.genre));

  addGenreButton("All");
  genres.forEach((genre) => addGenreButton(genre));

  renderBooks(books);
});
```

Compare this page with Exercise 1: the page logic barely changed, only the class names and HTML structure did. Bootstrap took over almost all of the styling, which is why `styles.css` dropped from about 80 lines to two rules.

**Challenge:** Add an About the Club button to the navbar that opens a modal saying "We meet every Tuesday at 7 PM at the Northside Library. New members are always welcome." Use the demo's Club Rules modal as your model. No JavaScript is needed.

Add the button inside the navbar's `container`, after the `navbar-brand` span. The navbar spreads its contents apart, so the button sits on the right.

```html
      <button type="button" class="btn btn-light btn-sm" data-bs-toggle="modal" data-bs-target="#about-modal">
        About the Club
      </button>
```

Add the modal after the closing `</main>` tag:

```html
  <div class="modal fade" id="about-modal" tabindex="-1" aria-labelledby="about-title" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h2 class="modal-title h5" id="about-title">About the Club</h2>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <p>We meet every Tuesday at 7 PM at the Northside Library. New members are always welcome.</p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
```

Make sure `data-bs-target` and the modal's `id` match exactly, including the `#`. A mismatch fails silently: the button does nothing, the same kind of silent failure as an empty jQuery selection in Week 11.

## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition, Mike Murach & Associates Inc., 2020. Chapter on jQuery effects and animations.
- jQuery API Documentation, Effects: https://api.jquery.com/category/effects/
- jQuery API Documentation, .animate(): https://api.jquery.com/animate/
- MDN Web Docs, prefers-reduced-motion: https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion
- MDN Web Docs, Using CSS transitions: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions
- Bootstrap 5.3 Documentation, Introduction: https://getbootstrap.com/docs/5.3/getting-started/introduction/
- Bootstrap 5.3 Documentation, Buttons: https://getbootstrap.com/docs/5.3/components/buttons/
- Bootstrap 5.3 Documentation, Cards: https://getbootstrap.com/docs/5.3/components/card/
- Bootstrap 5.3 Documentation, Grid system: https://getbootstrap.com/docs/5.3/layout/grid/
- Bootstrap 5.3 Documentation, Progress: https://getbootstrap.com/docs/5.3/components/progress/
- Bootstrap 5.3 Documentation, Modal: https://getbootstrap.com/docs/5.3/components/modal/
- W3Schools, jQuery Effects: https://www.w3schools.com/jquery/jquery_hide_show.asp
