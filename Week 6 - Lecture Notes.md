# Week 6: Images & Timers

## Working with Images in JavaScript

Every `<img>` element in the DOM has a `src` property, just like it has a `textContent` or `id` property. Because it is a regular DOM property, you can read it and change it with JavaScript the same way you learned in Week 4.

```html
<!DOCTYPE html>
<html>

<body>

    <img id="cmu_logo" src="cmu_logo.png">
    <script src="week_6.js"></script>

</body>
</html>
```

```javascript
const logo = document.getElementById("cmu_logo");
console.log(logo.src);

logo.src = "https://www.cmich.edu/images/default-source/presidents-division/university-communications/new-brand-images/we-do-tagline/we-do-tagline_nav-card_400x280.jpg?sfvrsn=37057cb6_4"

```

Setting `src` to a new file path tells the browser to load and display a different image immediately, no page refresh required. This is the foundation for everything you will build this week: rollovers, galleries, and slideshows all come down to swapping the `src` property at the right moment.

You can read or set the `alt` attribute the same way, which matters for accessibility and is worth updating any time the image content changes.

```javascript
logo.alt = "CMU Logo";
```

An image also carries its own dimensions, which JavaScript can read directly:

```javascript
console.log(logo.naturalWidth, logo.naturalHeight);
console.log(logo.width, logo.height);
```

`naturalWidth` and `naturalHeight` report the image file's actual size, while `width` and `height` report the size it is currently being displayed at on the page, which may be smaller if CSS has resized it. This distinction is worth knowing when you need to confirm an image loaded at full quality versus how it happens to look on screen.

## Preloading Images

The first time a browser needs an image, it has to request it from the server, which takes a moment. If you swap `src` to an image the browser has never loaded before, users can see a brief flash or delay. Preloading avoids this by loading the image into memory ahead of time, using a JavaScript `Image` object that is never actually added to the page.

Below is an image that takes some time to load:
```html
<!DOCTYPE html>
<html>

<body>

    <img id="mountains" width="600" src="https://images.unsplash.com/photo-1464822759023-fed622ff2c3b">
    <script src="week_6.js"></script>

</body>
</html>
```

In order to make sure the image loads before it can be shown in the `<img>`, preloading concept can be used.

```html
<!DOCTYPE html>
<html>

<body>

    <img id="mountains" width="600"><br>
    <button id="show">Show Mountains</button>
    <script src="week_6.js"></script>

</body>
</html>
```

```javascript
// Preload the image
const preload = new Image();
preload.src = "https://images.unsplash.com/photo-1464822759023-fed622ff2c3b";

// Wait for the user
document.getElementById("show").addEventListener("click", () => {
    document.getElementById("mountains").src = preload.src;
});
```

Once this code runs, the browser has already fetched the image and cached it. Later, when your script sets the image, the swap is instant because the browser does not need to go back to the server.

Preloading matters most for rollovers and galleries, where users expect an image to change the instant they act, not a moment later. If a page has several images that might be swapped in, it is common to preload each one when the page first loads:

```javascript
const preloadOne = new Image();
preloadOne.src = "images/photo1-large.jpg";

const preloadTwo = new Image();
preloadTwo.src = "images/photo2-large.jpg";
```

## Handling Image Load and Error Events

Images load asynchronously, meaning the browser keeps running your script while the image downloads in the background. If you need to know exactly when an image has finished loading, or whether it failed to load at all, you can listen for that the same way you listen for a click or a hover.

```javascript

const preload = new Image();
preload.src = "https://images.unsplash.com/photo-1464822759023-fed622ff2c3b";

preload.addEventListener("load", function () {
    console.log("image is ready to be displayed");
    
});

preload.addEventListener("error", function () {
    console.log("error loading the image");
    
});

const show = document.getElementById("show");
show.addEventListener("click", () => {
    document.getElementById("mountains").src = preload.src;
})
```

The `error` event is where the defensive coding habits from Week 5 apply directly to images. A missing file, a typo in a path, or a broken link should not leave a user staring at a broken image icon. Instead, catch the error and swap in a fallback image.

```javascript
const featured = document.getElementById("mountains");

featured.addEventListener("error", function () {
  featured.src = "images/placeholder.jpg";
  featured.alt = "Image unavailable";
});
```

## Image Rollovers

A rollover swaps an image when the user's mouse enters or leaves it, using the `mouseover` and `mouseout` events you first saw in Week 3 and wired up properly with `addEventListener()` in Week 4.

```html
<!DOCTYPE html>
<html>

<body>

    <img id="logo" width="200" src="https://www.cmich.edu/images/default-source/presidents-division/university-communications/new-brand-images/circle-action-c/color-variations/action-c-circle_color-variation_goldbg_400x280.jpg?sfvrsn=7faade51_4"><br>
    <script src="week_6.js"></script>

</body>
</html>
```

```javascript
const photo = document.getElementById("logo");

photo.addEventListener("mouseover", () => {
    photo.src = "https://www.cmich.edu/images/default-source/presidents-division/university-communications/new-brand-images/circle-action-c/color-variations/action-c-circle_color-variation_maroonbg_400x280.jpg?sfvrsn=d24ba084_4";
});

photo.addEventListener("mouseout", () => {
    photo.src = "https://www.cmich.edu/images/default-source/presidents-division/university-communications/new-brand-images/circle-action-c/color-variations/action-c-circle_color-variation_goldbg_400x280.jpg?sfvrsn=7faade51_4";
})

```

## Exercise (Thumbnail Preview Gallery)

You are given starter files for a product page with one large featured image and a row of three clickable thumbnails. Your job is to write the JavaScript that swaps the featured image when a thumbnail is clicked.

*index.html*
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Thumbnail Preview Gallery</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="viewer">
    <img id="featured-image" src="https://images.unsplash.com/photo-1533069027836-fa937181a8ce?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Featured product photo">
    <div class="thumbnails">
      <img id="thumb1" class="thumbnail" src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Thumbnail 1">
      <img id="thumb2" class="thumbnail" src="https://plus.unsplash.com/premium_photo-1719289799376-d3de0ca4ddbc?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Thumbnail 2">
      <img id="thumb3" class="thumbnail" src="https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Thumbnail 3">
    </div>
  </div>
  <script src="week_6.js"></script>
</body>
</html>
```

*styles.css*
```css
body {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 40px;
}

.viewer {
  max-width: 500px;
  margin: 0 auto;
}

#featured-image {
  width: 100%;
  border-radius: 8px;
}

.thumbnails {
  margin-top: 12px;
}

.thumbnail {
  width: 80px;
  height: 80px;
  object-fit: cover;
  margin: 0 6px;
  border-radius: 4px;
  cursor: pointer;
  border: 2px solid transparent;
}
```

Build corresponding `.js` so that:
- Clicking any thumbnail updates the featured image to that thumbnail's larger version.
- The three large photos are preloaded when the page loads, so the swap is instant.
- If the featured image ever fails to load, it falls back to original featured image.

### Solution

*week_6.js*
```javascript
const featured = document.getElementById("featured-image");
const thumb1 = document.getElementById("thumb1");
const thumb2 = document.getElementById("thumb2");
const thumb3 = document.getElementById("thumb3");

const preload1 = new Image();
preload1.src = "https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D";

const preload2 = new Image();
preload2.src = "https://plus.unsplash.com/premium_photo-1719289799376-d3de0ca4ddbc?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D";

const preload3 = new Image();
preload3.src = "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D";

thumb1.addEventListener("click", function () {
    featured.src = preload1.src;
});

thumb2.addEventListener("click", function () {
    featured.src = preload2.src;
});

thumb3.addEventListener("click", function () {
    featured.src = preload3.src;
});

featured.addEventListener("error", function () {
    featured.src = "https://images.unsplash.com/photo-1533069027836-fa937181a8ce?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D";
});


```

Each thumbnail gets its own click listener rather than a loop, since there are only three of them and each one maps to a specific large image. This keeps the code readable without needing anything beyond DOM selection and event listeners from Week 4.

**Challenge:** Add a visual highlight around whichever thumbnail is currently selected, so the user can see which photo is showing in the featured image. This requires adding a `.selected` style to `styles.css`:

```css
.thumbnail.selected {
  border-color: #333;
}
```

*week_6.js*
```javascript
const thumbnails = document.querySelectorAll(".thumbnail");

function selectThumbnail(selected) {
  thumbnails.forEach(function (thumb) {
    thumb.classList.remove("selected");
  });
  selected.classList.add("selected");
}

thumb1.addEventListener("click", function () {
  featured.src = "images/photo1-large.jpg";
  selectThumbnail(thumb1);
});

thumb2.addEventListener("click", function () {
  featured.src = "images/photo2-large.jpg";
  selectThumbnail(thumb2);
});

thumb3.addEventListener("click", function () {
  featured.src = "images/photo3-large.jpg";
  selectThumbnail(thumb3);
});
```

## Timers: setTimeout()

A timer lets you run code after a delay, or repeatedly, without the user clicking anything. The simplest timer is `setTimeout()`, which runs a function once, after a given number of milliseconds.

```javascript
setTimeout(function () {
  console.log("Three seconds have passed.");
}, 3000);
```

`setTimeout()` does not pause the rest of your script. The code after it keeps running immediately, and the function you passed in fires later, on its own. Try logging a message right before the `setTimeout()` call and another one inside it, then watch the order they print to the console.

`setTimeout()` returns a timer ID, which you can store in a variable and use to cancel the timer before it fires, with `clearTimeout()`.

```javascript
const timerId = setTimeout(function () {
  console.log("This will not run if cancelled in time.");
}, 5000);

clearTimeout(timerId);
```

## Repeating Actions: setInterval() and clearInterval()

`setInterval()` works like `setTimeout()`, except it keeps firing over and over, at the interval you specify, until you explicitly stop it.

```html
<!DOCTYPE html>
<html lang="en">
<body>
  <p id="timer">10</p>
  <button id="start">Start Timer</button>
  <script src="week_6.js"></script>
</body>
</html>
```

```javascript
let time = 10;

document.getElementById("start").addEventListener("click", () => {
    const timer = setInterval(() => {
        time--;
        document.getElementById("timer").textContent = time;

        if (time === 0) {
            clearInterval(timer);
            console.log("Time's up");
        }
    }, 1000);
});
```

Because `setInterval()` never stops on its own, always save the ID it returns so you can stop it later with `clearInterval()`. Forgetting to do this is a common source of bugs, such as code that keeps running in the background after a user leaves a feature, or a slideshow that cannot be paused.

## Building a Slideshow: Images and Timers Together

A slideshow combines everything above: a list of image files, a way to track which one is currently showing, and a timer that advances to the next one automatically.

*index.html*
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="viewer">
    <img id="featured-image" src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Featured product photo">
  </div>
  <script src="week_6.js"></script>
</body>
</html>
```
*styles.css*
```css
body {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 40px;
}

.viewer {
  max-width: 500px;
  margin: 0 auto;
}

#featured-image {
  width: 100%;
  border-radius: 8px;
}
```
*week_6.js*
```js
const images = [
    "https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://plus.unsplash.com/premium_photo-1719289799376-d3de0ca4ddbc?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
];

let currentIndex = 0;

function showImage(index) {
    const photo = document.getElementById("featured-image");
    photo.src = images[index];
}

function showNextImage() {
    currentIndex = (currentIndex + 1) % images.length;
    showImage(currentIndex);
}

const slideShowId = setInterval(() => {
    showNextImage();
}, 2000);

```

To hold the list of image filenames, this week's exercise uses an array, a way to store multiple values in a single variable. Arrays are not formally covered until Week 10, but the syntax needed here is small enough to use safely now.

>With the list in place, a counter variable tracks the current position, and the remainder operator (`%`) from Week 2 wraps the counter back to 0 once it passes the last image.

## Exercise (Photo Gallery Slideshow)

You are given starter files for a simple photo gallery page. The HTML and CSS are already built; your job is to write the JavaScript.

*index.html*
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Photo Gallery</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="gallery">
    <h1>Photo Gallery</h1>
    <img id="featured-image" src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Gallery photo">
    <div class="controls">
      <button id="prev-btn">Previous</button>
      <button id="next-btn">Next</button>
    </div>
  </div>
  <script src="week_6.js"></script>
</body>
</html>
```

*styles.css*
```css
body {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 40px;
}

.gallery {
  max-width: 500px;
  margin: 0 auto;
}

#featured-image {
  width: 100%;
  border-radius: 8px;
}

.controls {
  margin-top: 12px;
}

.controls button {
  padding: 8px 16px;
  margin: 0 6px;
  font-size: 16px;
}
```

Build `.js` so that:
- An array holds the paths to at least three images.
- A counter tracks which image is currently showing.
- The Next button advances to the next image, wrapping back to the first image after the last one.
- The Previous button moves back one image, wrapping to the last image if the user is on the first one.
- The gallery also advances automatically every two seconds, using `setInterval()`.

### Solution

*week_6.js*
```javascript
const images = [
    "https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://plus.unsplash.com/premium_photo-1719289799376-d3de0ca4ddbc?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
];

let currentIndex = 0;
const photo = document.getElementById("featured-image");
const btnPre = document.getElementById("prev-btn");
const btnNext = document.getElementById("next-btn");

function showImage(index) {
    photo.src = images[index];
}

function showNextImage() {
    currentIndex = (currentIndex + 1) % images.length;
    showImage(currentIndex);
}

function showPrevImage() {
    currentIndex = (currentIndex - 1 + images.length) % images.length;
    showImage(currentIndex);
}

btnPre.addEventListener("click", showPrevImage);
btnNext.addEventListener("click", showNextImage);

const slideShowId = setInterval(() => {
    showNextImage();
}, 2000);

```

The expression `(currentIndex - 1 + images.length) % images.length` handles the Previous button's wraparound. Subtracting 1 from index 0 would give -1, which is not a valid array position, so adding `images.length` first keeps the value positive before the remainder operator brings it back into range.

**Challenge:** Clicking Next or Previous while the slideshow is auto-advancing can feel jarring, since the image the user just chose gets replaced a moment later by the timer. Add a Play/Pause button that starts and stops the automatic slideshow. This requires adding a button to `index.html`:

```html
<button id="play-pause-btn">Pause</button>
```

*week_6.js*
```javascript
const images = [
    "https://images.unsplash.com/photo-1542291026-7eec264c27ff?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://plus.unsplash.com/premium_photo-1719289799376-d3de0ca4ddbc?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
    "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=1170&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
];

let currentIndex = 0;
const photo = document.getElementById("featured-image");
const btnPre = document.getElementById("prev-btn");
const btnNext = document.getElementById("next-btn");

function showImage(index) {
    photo.src = images[index];
}

function showNextImage() {
    currentIndex = (currentIndex + 1) % images.length;
    showImage(currentIndex);
}

function showPrevImage() {
    currentIndex = (currentIndex - 1 + images.length) % images.length;
    showImage(currentIndex);
}

btnPre.addEventListener("click", showPrevImage);
btnNext.addEventListener("click", showNextImage);

let slideshowId = setInterval(showNextImage, 2000);
let isPlaying = true;

const playPauseBtn = document.querySelector("#play-pause-btn");

playPauseBtn.addEventListener("click", function () {
  if (isPlaying) {
    clearInterval(slideshowId);
    playPauseBtn.textContent = "Play";
  } else {
    slideshowId = setInterval(showNextImage, 2000);
    playPauseBtn.textContent = "Pause";
  }
  isPlaying = !isPlaying;
});
```

## References

- Delamater, M., Murach's JavaScript and jQuery, 4th Edition
  - Chapter 7: How to work with images and timers
- W3Schools Tutorials
  - [HTML DOM Image Object](https://www.w3schools.com/jsref/dom_obj_image.asp)
  - [JavaScript Timing Events](https://www.w3schools.com/js/js_timing.asp)
