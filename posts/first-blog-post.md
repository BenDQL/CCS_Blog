---
title: This is my first blog post!
published_at: 2024-03-06
snippet: This is my first session to learn a coding! Hello, world!
disable_html_sanitization: true
---

## This is the first week homework 1.0

<iframe src="https://editor.p5js.org/BenDQL/full/D0_Eyr6lr" width="500" height="200"></iframe>
In the first lesson, our teacher use for loop to create a line of squares by using p5.js. Beased on this sketch, we need to use for loop to create a grid.

```js
function setup() {
  createCanvas(innerWidth, 200);
  colorMode(HSB);
  rectMode(CENTER);
  strokeWeight(1);
}

function draw() {
  const t = frameCount / 20;
  background("turquoise");
  noFill();
  const total_squares = 10;
  const size = width / total_squares;

  const total_lines = 5; //create 5 lines

  for (let j = 0; j < total_lines; j++) {
    for (let i = 0; i < total_squares; i++) {
      square(
        (width * (i + 0.5)) / total_squares,
        20 + 50 * j,
        (t * (i + j + 5)) % size
      );
    }
  }
}
```

At first, I need to make the square to transparent `noFill()`, and add the stroke `strokeWeight(1)`. After that, I need to create another 4 line (total 5 lines). Using 2 `for ` loops: inter `for` loop is to create 10 squares in one line; out `for` loop is to create 5 lines.

## This is the first week homework 1.1

[Remotely Distant](https://www.remotelydistant.com/)

<img title="Remotely Distant" alt="Remotely Distant" src="/240306_first_post/Remotely_Distant.png">

I try to use p5.js to create the similar sketch for "Remotely Distant". The sketch below is I create by using p5.js.

<iframe src="https://editor.p5js.org/BenDQL/full/TFdCDYfIy"width="600" height="650"></iframe>

At the biginning, I see that there are three different color gradient. Base on lesson study, I use color gradient funcion to make one color gradient. After that, I copy this function twice
and change their color and position. finally, I make a similar sketch to "Remotely Distant".

```js
function setup() {
  createCanvas(600, 600);
}

function draw() {
  background("black");
  noStroke();

  const colour_1 = color("green");
  const colour_2 = color("yellow");
  for (let i = 0; i < 600; i++) {
    let p = (i + frameCount) / 600;
    p = p % 0.5;
    const c = lerpColor(colour_1, colour_2, p);
    strokeWeight(1);
    stroke(c, 100, 100);
    line(i, 0, i, height / 2);
  }

  const colour_3 = color("blue");
  const colour_4 = color("green");
  for (let i = 0; i < 600; i++) {
    let p = (i + frameCount) / 400;
    p = p % 0.5;
    const c = lerpColor(colour_3, colour_4, p);
    strokeWeight(1);
    stroke(c, 100, 100);
    line(i, height / 2, i, (height * 3) / 4);
  }

  const colour_5 = color("yellow");
  const colour_6 = color("pink");
  for (let i = 0; i < 600; i++) {
    let p = (i + frameCount) / 600;
    p = p % 0.5;
    const c = lerpColor(colour_5, colour_6, p);
    strokeWeight(1);
    stroke(c, 100, 100);
    line(i, (height * 3) / 4, i, height);
  }
}
```

When I finish this sketch, I also find a conventient way to achieve this effect. From the code above, I find that in the duplicate code, we we only need to change a few elements like: color, position. So we can wrap thr code into `function`"slidingColours" than call the `slidingColours()`with arguments of `colour1, colour2, y1, y2, pFactor, lines` within `draw ()`.

```js
function setup() {
  createCanvas(600, 600);
}

function slidingColours(colour1, colour2, y1, y2, pFactor, lines) {
  const colour_1 = colour1;
  const colour_2 = colour2;
  for (let i = 0; i < lines; i++) {
    let p = (i + frameCount) / lines;
    p = p % pFactor;
    const c = lerpColor(colour_1, colour_2, p);
    strokeWeight(1);
    stroke(c, 100, 100);
    line(i, y1, i, y2);
  }
}

function draw() {
  background("black");
  noStroke();

  slidingColours(color("red"), color("blue"), 0, height / 2, 0.5, 600);
  slidingColours(
    color("deeppink"),
    color("darkturquoise"),
    height / 2,
    (height * 7) / 8,
    0.25,
    700
  );
  slidingColours(
    color("red"),
    color("blue"),
    (height * 7) / 8,
    height,
    0.5,
    600
  );
}
```

**bold** -->
