---
title: This is my first blog post!
published_at: 2024-03-06
snippet: This is my first session to learn a coding! Hello, world!
disable_html_sanitization: true
---

## This is the first week homework 1.0

<iframe src="https://editor.p5js.org/BenDQL/full/D0_Eyr6lr" width="500" height="200"></iframe>
In the first lesson, our teacher use for loop to create a line of squares by using p5.js. Beased on this sketch, we need to use for loop to create a grid.

```
function setup() {
  createCanvas(innerWidth, 200);
  colorMode(HSB);
  rectMode(CENTER);
  strokeWeight(1);
}

function draw() {
  const t = frameCount / 20
  background('turquoise');
  noFill();
  const total_squares = 10;
  const size = width / total_squares;

  const total_lines = 5;  //create 5 lines

  for (let j = 0; j < total_lines; j++){
    for (let i = 0; i < total_squares; i++) {
      square(width * (i + 0.5) / total_squares, 20 + 50 * j, t * (i + j + 5) % size);
    }
  }
}
```

At first, I need to make the square to transparent `noFill()`, and add the stroke `strokeWeight(1)`. After that, I need to create another 4 line (total 5 lines). Using 2 `for ` loops: inter `for` loop is to create 10 squares in one line; out `for` loop is to create 5 lines.

## This is the first week homework 1.1

[Remotely Distant](https://www.remotelydistant.com/)

<img title="Remotely Distant" alt="Remotely Distant" src="/static/240306_first_post/Remotely_Distant.png">

_underline_

**bold** -->
