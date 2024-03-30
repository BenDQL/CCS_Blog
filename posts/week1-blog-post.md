---
title: Week 1 - Intro to p5
published_at: 2024-03-06
snippet: This is my first blog post for creative coding.
disable_html_sanitization: true
---

## Create A Grid in P5.js

<iframe src="https://editor.p5js.org/BenDQL/full/D0_Eyr6lr" width="500" height="200"></iframe>

In the first sketch, it uses a `for` loop to create 10 squares in a line. Based on the code, to create a grid, I need to use the nested loops - the outer `for` loop to generate lines, while the inner `for` loop to generate squares for each line.

I learned this technique from this [Youtube p5.js coding tutorial](https://www.youtube.com/watch?v=UKxB2j4h7Ag).

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

In this code, I firstly make the square transparent by calling `noFill()`, and add the stroke using `strokeWeight(1)`. Using nested `for` loop so that the outer `for` loop will create 5 lines in total, and the inner `for` loop will create 10 squares in each line.

## Replicate Rafaël Rozendaal’s "remotely distant .com"

[Remotely Distant](https://www.remotelydistant.com/)

<img title="Remotely Distant" alt="Remotely Distant" src="/240306_first_post/Remotely_Distant.png">

<p></p>

When I saw this artwork, I think it can be divided into 3 bands from canvas top to bottom. For each band, it has colour gradients and the band looks like it's moving from right to left with slight different speed. I think this effect can be achieved by drawing multiple lines with different stroke colour within each band, so that all the lines comprises a colour gradient band. Then after each draw, we need to update each line's colour and redraw all the lines, so the gradient band looks like moving from right to left. In class, I learned that using `frameCount` can modify the colour after every draw / frame.

I replicated the work of "Remotely Distant" in P5.js sketch as the following.

<iframe src="https://editor.p5js.org/BenDQL/full/TFdCDYfIy" width="600" height="650"></iframe>

At the beginning, I saw that there are three different colour gradients. Based on my study in class, I knew that I could achieve colour gradient effect using `lerpColor` in p5.js. So I create one band for the colour gradient, with lines with colours drawing from the left to right, to form the gradient band. Then I copied the code 3 times, changing its colour for gradient effect and its position, generating 3 moving gradient bands with different colours.

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

Upon completing the code as above, I noticed several lines of code were duplicated, differing only in the elements passed as arguments into the functions, such as colour and position. Learning from this [p5.js example](https://p5js.org/examples/structure-functions.html), I found that I could wrap the duplicate code into a custom function called `slidingColours()` then call the `slidingColours()`with arguments of `colour1, colour2, y1, y2, pFactor, lines` within `draw()` to remove the duplication. Also, I modified the colour from 2 colour gradients to multiple colour gradients by using HSB colour mode and updating the hue. Then I got inspiration from reading the code again, I assumed that I could make those gradient bands moving with different speed by incrementing `h` value with different ratio after every draw, and I called the . So I had a try and found it's working.

```js
function setup() {
  createCanvas(600, 600);
}

function slidingColours(y1, y2, ratio, lines) {
  colorMode(HSB);
  for (let i = 0; i < 600; i++) {
    const p = (i + ratio * 600) / 600;
    let h = p * 360;
    h += frameCount * ratio;
    h %= 360;
    strokeWeight(1);
    stroke(h, 100, 100);
    line(i, y1, i, y2);
  }
}

function draw() {
  background("black");
  noStroke();

  slidingColours(0, height / 2, 0.5, 600);
  slidingColours(height / 2, (height * 7) / 8, 0.25, 700);
  slidingColours((height * 7) / 8, height, 0.1, 800);
}
```

---
