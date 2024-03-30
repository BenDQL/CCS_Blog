---
title: Week 2 - Basic Javascript Concepts
published_at: 2024-03-14
snippet: Refactor Falling Falling's code and respond to Rafaél Rozendaal's "only suddenly.com"
disable_html_sanitization: true
---

## Refactor "Falling Falling" Code

[The complete code after refactoring](https://editor.p5js.org/BenDQL/sketches/yzut3curD)

First, I create a class called `Faller`, and moves faller's attributes to the class.
Then I move the draw logic of Faller from `sketch.js` to `draw()` method within the `Faller` class. The final `Faller` class looks like as follow:

```js
class Faller {
  constructor(colours) {
    this.colours = colours;
    this.start_points = [
      { x: 0, y: height / 2 },
      { x: 0, y: 0 },
      { x: width / 4, y: 0 },
      { x: width / 2, y: 0 },
      { x: (width * 3) / 4, y: 0 },
      { x: width, y: 0 },
      { x: width, y: height / 2 },
    ];
    let end_points = [];
    for (let i = 1; i < 8; i++) {
      end_points.push({
        x: (i * width) / 8,
        y: height,
      });
    }
    this.end_points = end_points;
    this.curves = new Array(7).fill().map(rand_curve);
    this.phase = 0;
  }

  draw() {
    colorMode(RGB);
    fill(lerpColor(this.colours[0], this.colours[1], this.phase));
    beginShape();
    vertex(0, height);
    this.start_points.forEach((s, i) => {
      // Find the next point to draw
      // between start_point and its corresponding end_point
      const p = find_point(s, this.end_points[i], this.phase ** this.curves[i]);
      vertex(p.x, p.y);
    });
    vertex(width, height);
    endShape();
    this.phase += 0.008;
  }
}
```

Because in the `Faller` class, it will use `find_point()`, `rand_col()` and `rand_curve()` functions, so I need to move them into `Faller.js`

```js
// Find the (x, y) for the next point
function find_point(start, end, phase) {
  const delt = {
    x: end.x - start.x,
    y: end.y - start.y,
  };
  const x = start.x + delt.x * phase;
  const y = start.y + delt.y * phase;
  return { x, y };
}

// Generate random colour code
function rand_col() {
  colorMode(HSB);
  const h = floor(random() * 360);
  return color(h, 100, 100);
}

// Generate random curve
function rand_curve() {
  return random() * 2 + 1;
}
```

In `setup()` function of `sketch.js`, initialise the first `faller` object and push it into the `fallers` array.

```js
// Initially the faller's colour has 2 randomly generated colour
const faller = new Faller([rand_col(), rand_col()]);
fallers.push(faller); // Add the first faller into the fallers array
```

The following lines of code aims to add the new faller to the start of the `fallers` array:

```js
fallers.reverse();
fallers.push(new_faller);
fallers.reverse();
```

But I found it can be done by using the `unshift()` function of array, so I changed them to be:

```js
if (frameCount % 40 == 0) {
  const new_faller = new Faller([bg, rand_col()]); // The new faller should have the bg colour and 1 randomly generated colour
  fallers.unshift(new_faller); // Insert new faller at the start of the fallers array
  bg = rand_col(); // Generate new background colour
}
```

In the `draw()` of `sketch.js`, it needs to iterate the `fallers` array and draws all the fallers. It can be updated to directly call the `f.draw()` to serve the same purpose, as the draw logic has been moved into the `draw()` method of the `Faller` class now.

And the final `sketch.js` looks like as follow:

```js
const fallers = [];
let bg;

function setup() {
  createCanvas(innerWidth, innerHeight);
  noStroke();
  colorMode(HSB);

  // Set up the first faller
  // Initially the faller's colour has 2 randomly generated colour
  const faller = new Faller([rand_col(), rand_col()]);
  // Add the first faller into the fallers array
  fallers.push(faller);

  bg = rand_col();
}

function draw() {
  background(bg);

  if (frameCount % 40 == 0) {
    // The new faller should have the bg colour and 1 randomly generated colour
    const new_faller = new Faller([bg, rand_col()]);
    // Insert new faller at the start of the fallers array
    fallers.unshift(new_faller);
    bg = rand_col(); // Generate new background colour
  }

  const redundant = [];

  // Draw each faller
  fallers.forEach((f, i) => {
    f.draw();
    // When the faller is completed,
    // add it to the redundant array for removal later
    if (f.phase > 1) redundant.push(i);
  });

  // Remove the completed faller from the fallers array
  redundant.forEach((n) => fallers.splice(n, 1));
}
```

## Respond to Rafaél Rozendaal's "only suddenly.com"

[only suddenly .com - Rafaël Rozendaal](https://www.onlysuddenly.com/)

<iframe src="https://www.onlysuddenly.com"width="800" height="650"></iframe>

I intend to create a similar piece in p5.js where a ball moves within the canvas. When the ball hits the canvas edge, it will bounce, and the colors of the ball le and the canvas' background will change their colours accordingly.

I create a class called `Ball`, which has `x`, `y`, `xSpeed`, `ySpeed` and `radius` as attributes. It has a `draw()` method to draw the ball base on those elements. So when the ball moves, its position (x, y) will change, and a ball will be drawn based on its updated position. I also create a `updateColour()` method within the class, which will randomly generate a colour code and assign to the ball's colour attribute.

```js
class Ball {
  constructor(x, y, xSpeed, ySpeed, radius) {
    this.x = x;
    this.y = y;
    this.xSpeed = xSpeed;
    this.ySpeed = ySpeed;
    this.radius = radius;
    this.colour = randomColour();
  }

  updateColour() {
    this.colour = randomColour();
  }

  draw() {
    fill(this.colour);
    ellipse(this.x, this.y, this.radius * 2, this.radius * 2);

    // Move the circle
    this.x += this.xSpeed;
    this.y += this.ySpeed;
  }
}

function randomColour() {
  colorMode(HSB);
  const h = floor(random() * 360);
  return color(h, 100, 100);
}
```

I found some tutorial online about bouncing ball effect. I learned how to make the ball bounce when it hits the canvas edge and change direction. The techinique is basically checking the ball's current position (x, y). For x, it compares with `width - ball.radius` or `ball.radius`, this is the x position when the ball just approaches to the canva's right and left edge, if `ball.x > width - ball.radius` or `ball.x < ball.radius`, it means the ball has exceeds the edge, in this case, the ball's moving direction needs to be updated, towards the opposite directioin by updating `ball.xSpeed = -ball.xSpeed`.

[The Bouncing Ball - p5.js Tutorial](https://www.youtube.com/watch?v=LO3Awjn_gyU)

[Bouncing ball by icm](https://editor.p5js.org/icm/sketches/BJKWv5Tn)

After that, I need to enable the ball and canvas to change colour when the ball hits the canvas edge. In `sketch.js`, within the `draw()` function I call `ball.updateColour()` to update the ball's colour and update canvas' background colour `bgColour` by assigining with a new colour code.

```js
const xSpeed = 3;
const ySpeed = 2;

let bgColour;
let ball;

function setup() {
  createCanvas(800, 400);
  colorMode(HSB);
  noStroke();

  // Initialise the background colour and the ball
  bgColour = randomColour();
  ball = new Ball(width / 2, height / 2, 3, 2, 60);
}

function draw() {
  background(bgColour);
  ball.draw();

  // When hitting the edge, change the direction of move & update bgColour + circleColour
  if (ball.x > width - ball.radius || ball.x < ball.radius) {
    ball.xSpeed = -ball.xSpeed;
    ball.updateColour();
    bgColour = randomColour();
  }
  if (ball.y > height - ball.radius || ball.y < ball.radius) {
    ball.ySpeed = -ball.ySpeed;
    ball.updateColour();
    bgColour = randomColour();
  }
}
```

<iframe src="https://editor.p5js.org/BenDQL/full/PUXGMg-7C"width="800" height="400"></iframe>
