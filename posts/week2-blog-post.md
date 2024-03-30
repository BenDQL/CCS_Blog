---
title: Week 2 blog post!
published_at: 2024-03-14
snippet: week 2 homework
disable_html_sanitization: true
---

## This is the first week homework 2.0

## Respond

[only suddenly .com - Rafaël Rozendaal](https://www.onlysuddenly.com/)

<iframe src="https://www.onlysuddenly.com"width="800" height="650"></iframe>

When I saw Rafaël Rozendaal’s captivating artwork "only suddenly .com", It involves a circle moving randomly within a rectangle. When the circle touches the edges of the rectangle, it will rebounds. Additionally, the colours of the circle, rectangle, and the area outside the rectangle will change.

I intend to create a similar piece in p5.js where a circle moves uniformly within the canvas. When the circle makes contact with the canvas edges, it will rebounds, and the colors of the circle and the canvas change accordingly.

I create a class called `Ball`, which has `x`, `y`, `xSpeed`, `ySpeed` and `radius` as attributes. It has a `draw()` method to draw the cricle base on those elements. I also create a `updateColour()` method and call the `randomColour()` function to create a new colour and to fill the ball.

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

I found some tutorial online about bouncing ball. I learned how to make the ball move within a canvas and change direction.

[The Bouncing Ball - p5.js Tutorial](https://www.youtube.com/watch?v=LO3Awjn_gyU)

[Bouncing ball by icm](https://editor.p5js.org/icm/sketches/BJKWv5Tn)

After that, I need to make the ball and canvas change to different colour when the ball touch the canvas edge. In `draw()`function I add `updateColour()` to fill the ball and use `randomColour()` function to create a new colour for the `bgColour`. So that there are two different colour will be respond and fill to the ball and canvas when the ball touch the canvas edge.

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
