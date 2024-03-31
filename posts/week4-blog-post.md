---
title: Week 4 - Fractals
published_at: 2024-03-27
snippet: Create a hexagon fractal using recursion & chaos
disable_html_sanitization: true
---

## Create A Hexagon Fractal Using Recursion

<img title="Hexagon Fractal" alt="Hexagon Fractal" src="/240306_first_post/hexagon_fractal.png">

<p></p>

[The complete code](https://github.com/BenDQL/Fractals_net_art/tree/main)

Based on this [draw_squares example](https://blog.science.family/240327_canvas_example), I learnt how to draw similar squares with different colour and size to form a fractal. I want to enhance this example by using hexagons.

But I checked the Canvas APIs, and I couldn't find a function to draw the hexagons directly. So I searched online on how to draw a hexagon in canvas and checked a couple of tutorials.

From [this tutorial](https://eperezcosano.github.io/hex-grid/), I also learnt that drawing a hexagon on canvas depends on these attributes: the center position (x, y) and the radius of the outer circle:

<img title="Hexagon Drawing Technique" alt="Hexagon Drawing Technique" src="/240306_first_post/hexagon_draw.png">

Therefore, I can create a `draw_hexagon` function, with 3 arguments: x, y and r to represent the center position and the radius, to control the hexagon's size and position being drawn on canvas. Utilise the `rand_col()` function, it can randomly generate a colour code, and this colour will be used for filliing the hexagon by calling `ctx.fill()`. The `draw_hexagon()` looks as follow:

```js
function draw_hexagon(r, x, y) {
  ctx.fillStyle = rand_col();
  ctx.beginPath();
  for (var i = 0; i < 6; i++) {
    ctx.lineTo(x + r * Math.cos(a * i), y + r * Math.sin(a * i));
  }
  ctx.closePath();
  ctx.fill();
}
```

To create the fractal, I am thinking to make the hexagons drawing from the bigger to smaller, as the smaller ones will draw on top of the bigger ones. And I'd like to move the hexagons to the top-right corner as they are drawing - that means the next hexagon to be drawn, it will has a smaller size (with 20 less for its radius) and the center position will move to the right (with +5 for its x) and top (with -8 for its y) a little bit, compared to the previous one. These hexagons will be continued on drawing until its size becomes zero (start_radius becomes 0 or less). In this case, I need to write the `draw_hexagons()` function, and it requires to call the `draw_hexagons()` within it - using recursion to keep hexagons drawing until its size becomes 0. The `draw_hexagons()` looks as follow:

```js
function draw_hexagons(start_radius, start_x, start_y) {
  draw_hexagon(start_radius, start_x, start_y);

  if (start_radius > 0) {
    draw_hexagons(start_radius - 20, start_x + 5, start_y - 8);
  }
}
```

The first hexagon to draw should start from the center of the canvas, with (x, y) equal to (cnv.width / 2, cnv.height / 2) and radius starts from cnv.width / 2. And I also want to redraw the hexagon fractal when screen resizes. So I tweak the `window.onresize` handler and call the `draw_hexagons()` function for the redraw. The `script.js` file looke as follow:

```js
const cnv = document.getElementById(`recursive_squares`);
cnv.width = cnv.parentNode.scrollWidth;
cnv.height = cnv.width;
const a = (2 * Math.PI) / 6; // Use for drawing hexagon

const ctx = cnv.getContext(`2d`);

function rand_col() {
  return `hsl(${Math.random() * 360}, 100%, 66%)`;
}

// Draw hexagon with radius (define size)
function draw_hexagon(r, x, y) {
  ctx.fillStyle = rand_col();
  ctx.beginPath();
  for (var i = 0; i < 6; i++) {
    ctx.lineTo(x + r * Math.cos(a * i), y + r * Math.sin(a * i));
  }
  ctx.closePath();
  ctx.fill();
}

function draw_hexagons(start_radius, start_x, start_y) {
  draw_hexagon(start_radius, start_x, start_y);

  if (start_radius > 0) {
    draw_hexagons(start_radius - 20, start_x + 5, start_y - 8);
  }
}

draw_hexagons(cnv.width / 2, cnv.width / 2, cnv.height / 2);

window.onresize = () => {
  // When screen resize, redraw the hexagons based on updated screen size
  cnv.width = innerWidth;
  cnv.height = innerHeight;
  draw_hexagons(cnv.width / 2, cnv.width / 2, cnv.height / 2);
};
```

## Chaos

Through my studies, I discover that using recursion in algorithms can easily generate shapes with diverse attributes, such as color and size. These attributes can be randomly assigned, resulting in each shape exhibiting unique patterns. Moreover, shapes can be positioned according to specific rules or sequences, and this has introduced some extend of chaos. Expanding on this concept, we can further increase complexity by generating more different shapes as fractals on the canvas. By arranging various shapes with different attributes according to predefined rules, we can effectively maximise chaos.

For assignment 2, I got to know that Glitch Art is considered part of the Post-Digital. I am going to create a glitch-style piece. I can employ the above technique to generate a combination of some shapes, then add effects such as movement, flickering, or displacement to those shapes, making the work appear more like glitch-style pieces.

---
