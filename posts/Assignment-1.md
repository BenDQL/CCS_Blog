---
title: Assignment 1
published_at: 2024-03-27
snippet: Assignment 1 design
disable_html_sanitization: true
---

## Replicate the work from Rafaël Rozendaal

[Return Reverse](https://www.returnreverse.com/)

<img title="Return Reverse" alt="Return Reverse" src="/240306_first_post/Return_Reverse.png">

how did I recreate this artwork on p5.js? My ideas is to make it by 3 steps: 1. create a static radial gradient effect; 2. make the effect moving from inside to outside; 3. Repeat the effect generatioin and animation.

### 1. Create a static radial gradient effect

Firstly, my idea was to draw many circles on the canvas. Each circle has the same center but with increasing radius and slightly different colour. I use the center of the canvas as the center of the circles. The radius of each circle incremenets by 1 and has a different hue. Then, we obtain a static image composed of many circles of different colors as follow:

<iframe src="https://editor.p5js.org/BenDQL/full/jgRcqE2ce"width="600" height="650"></iframe>

I create a class called `Circle`, which has `radius`,`hue`,`x` and `y` as attributes. It has a `draw()` method to draw the cricle base on its center perstion(x, y), radius and hue.

```js
class Circle {
  constructor(radius, hue) {
    this.x = width / 2;
    this.y = height / 2;
    this.radius = radius;
    this.hue = hue;
  }

  draw() {
    fill(this.hue, 90, 90);
    ellipse(this.x, this.y, this.radius, this.radius);
  }
}
```

I aslo create a `function` to calculate the maximum radius of circle to be drawn on the canvas, which will be used in the `sketch.js`.

```js
// Calculate the max radius to draw according to width & height
function calculateMaxRadius() {
  const x = width / 2;
  const y = height / 2;
  return floor(sqrt(x * x + y * y));
}
```

Then in the `sketch.js`, I change to the HSB mode and colour value between 0 and maxRadius for easier colour manipulation among circles by calling `colorMode(HSB, maxRadius, 100, 100)`. Using `for ()` to generate the number of `maxRadius` circles, and assign each cricle with a slightly different hue, so to create the radial gradient effect by drawing those circles.

```js
const circles = [];

function setup() {
  createCanvas(innerWidth, innerHeight);
  background(0);
  noStroke();
  ellipseMode(RADIUS);

  // Change to HSB and colour value between 0 and maxRadius
  const maxRadius = calculateMaxRadius();
  colorMode(HSB, maxRadius, 100, 100);

  let hue = random(0, maxRadius);
  for (let r = 0; r <= maxRadius; r++) {
    // For each circle, have a slightly different hue for Radial Gradient effect
    hue = (hue + 1) % maxRadius;
    const circle = new Circle(r, hue, maxRadius);
    circles.push(circle); // Add it to circles array
  }
}

function draw() {
  for (let i = circles.length - 1; i >= 0; i--) {
    circles[i].draw();
  }
}
```

### 2. make the effect moving from inside to outside

Based on Step1, I just to make those cricles radius incremenets by 1 afet every draw, so each cricle will become larger and larger.

<iframe src="https://editor.p5js.org/BenDQL/full/lFU-wzkGa"width="600" height="650"></iframe>

After drawing a circle, its increated by 1 by calling the `updateRadius` method. Afer every draw, the cricle radius will increase by 1.

```js
class Circle {
  constructor(radius, hue, maxRadius) {
    this.x = width / 2;
    this.y = height / 2;
    this.radius = radius;
    this.hue = hue;
    this.maxRadius = maxRadius;
  }

  updateRadius() {
    this.radius += 1; // Increase the radius after every draw
  }

  draw() {
    fill(this.hue, 90, 90);
    ellipse(this.x, this.y, this.radius, this.radius);
  }
}
```

Then in the `sketch.js`, `updateRadius()` method will be called after evey draw of each cricle.

```js
function draw() {
  for (let i = circles.length - 1; i >= 0; i--) {
    circles[i].draw();
    circles[i].updateRadius();
  }
}
```

### 3. Repeat the effect generatioin and animation.

<iframe src="https://editor.p5js.org/BenDQL/full/2FvpealFh"width="600" height="650"></iframe>

Once a circle's radius exceeds the `maxRadius` vaule, the circle will becaome much larger than the canvas size, and that means the circle won't be seen by the viewers. Therefore, the radius of those circles should be reset to 0. This ensure that each circle gradually becaomes larger, than repeat the animation again from a small circle on the center of the canvas, over and over again.

```js
  updateRadius() {
    this.radius += 1;    // Increase the radius after every draw
    if (this.radius > this.maxRadius) {
      this.radius = 0;     // Reset radius if it exceeds maxRadius value
    }
  }
```

However I nedd to ensure that, the smaller cricle will the top of the bigger ones. When the most outside circle radius reset to 0, it still stays as the last element of the `circles` array. It needs to be moved to be the first element, so when drawing the circles from the end to the start within the `circles` array acts the same as drawing circles from larger to smaller. Therefore, `circles`array should be sorted by their radius value ascendingly.

```js
function draw() {
  // Sort circles by their radius value ascendingly
  circles.sort((circleA, circleB) => circleA.radius - circleB.radius);

  for (let i = circles.length - 1; i >= 0; i--) {
    circles[i].draw();
    circles[i].updateRadius();
  }
}
```

## Respond "Return Reverse.com"

<iframe src="https://editor.p5js.org/BenDQL/full/HhM9c54sy"width="600" height="650"></iframe>

Base on the above code, I create a class called `GradientObject`, to repersent one radial gradient effect object. Each `GradientObject` will have a random center persition and will maintain a `circles` array for drawing the radial gradient effect. Also the `GradientObject`have a `maxRadius` to control its size. It has a `draw()` method to draw many circles from its `circles` array for the gradient effect, which is the same as the `draw()` function in `sketch.js` in last session of replicating the "Return Reverse.com". I also write a `generateCircles()` method for generating the circle elements for `circles` array.

Please note that `GradientObject` has a `edge` attribute, which is used for the animation of growing the object from inside to outside when it appears.

```js
class GradientObject {
  constructor(maxRadius) {
    this.maxRadius = maxRadius;
    this.edge = 0;
    // Randomly set a position
    this.x = random(0, width);
    this.y = random(0, height);
    this.circles = this.generateCircles();
  }

  generateCircles() {
    // Initialise the circle array
    const circles = [];
    let hue = random(0, this.maxRadius);
    for (let r = 0; r <= this.maxRadius; r++) {
      // For each circle, have a slightly different hue for Radial Gradient effect
      hue = (hue + 1) % this.maxRadius;
      const circle = new Circle(this.x, this.y, r, hue, this.maxRadius);
      circles.push(circle); // Add it to circles array
    }
    return circles;
  }

  draw() {
    // Sort circles by their radius value ascendingly
    this.circles.sort((circleA, circleB) => circleA.radius - circleB.radius);

    for (let i = this.circles.length - 1; i >= 0; i--) {
      if (this.circles[i].radius <= this.edge) {
        // Draw circles within edge
        this.circles[i].draw();
      }
      this.circles[i].updateRadius();
    }

    // Increase the edge radius for the next frame
    this.edge = min(this.edge + 1, this.maxRadius);
  }
}
```

Now for each `GradientObject`, its radius is set to be 180 instead of covering the whole canvas. Initially, there will be 2 `GradientObject` appearing one by one with the time lag of 2 seconds. And the animation of `GradientObject` will last for 4 seconds, then it will stop, and at the same time, a new `GradientObject` will be created and appear on the canvas.

Because at a time, there will be at least 2 `GradientObject` shown on the canvas, I need to use the `gradientObjects` array to store the state of all the `GradientObject`s. And a timer is required - whenever it hits 4 seconds, we need to stop the animation for the corresponding `GradientObject` by removing it from the `gradientObjects` array, and the timer will be reset for the next 4 seconds. Therefore, in the `sketch.js` file, the `draw()` function now has the logic to check for 4 seconds to stop the animation and reset the timer.

```js
const gradientObjects = [];
const maxRadius = 180;
let timer = 0; // Timer to control the duration of the animation
const duration = 4; // Each gradient objects will last 4 seconds

function setup() {
  createCanvas(innerWidth, innerHeight);
  background(0);
  noStroke();
  ellipseMode(RADIUS);

  // Change to HSB and colour value between 0 and maxRadius
  colorMode(HSB, maxRadius, 100, 100, maxRadius);

  // Generate 2 gradient objects with 2-second lag initially
  generateGradientObject();
  setTimeout(() => generateGradientObject(), (duration / 2) * 1000);
}

function draw() {
  gradientObjects.forEach((gradientObject) => {
    gradientObject.draw();
  });

  // Update the timer
  timer++;

  // If the timer exceeds duration, reset the animation
  if (timer >= duration * 60) {
    // Assuming 60 frames per second
    timer = 0;
    gradientObjects.shift(); // Remove the first gradient object in the array
    generateGradientObject(); // Generate a new gradient object
  }
}

function generateGradientObject() {
  const gradientObject = new GradientObject(maxRadius);
  gradientObjects.push(gradientObject);
}
```

**bold** -->
