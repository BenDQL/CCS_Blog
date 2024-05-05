---
title: Week 7 - Combining Ideas
published_at: 2024-04-24
snippet: Combining ideas with ascii cam
disable_html_sanitization: true
---

## Combine An Example with Ascii Cam

<script src="/scripts/c2.min.js"></script>

<div>Quad Tree Example from c2.js: </div>
<canvas id="c2"></canvas>
<p></p>
<div>Combine it with ascii cam: </div>
<div id="ascii_div"></div>

<script>
const renderer = new c2.Renderer(document.getElementById('c2'));
resize();

renderer.background('#cccccc');
let random = new c2.Random();


class Agent extends c2.Circle{
    constructor() {
        let x = random.next(renderer.width);
        let y = random.next(renderer.height);
        let r = random.next(10, renderer.width/15);
        super(x, y, r);

        this.vx = random.next(-2, 2);
        this.vy = random.next(-2, 2);
        this.color = c2.Color.hsl(random.next(0, 30), random.next(30, 60), random.next(20, 100));
    }

    update(){
        this.p.x += this.vx;
        this.p.y += this.vy;

        if (this.p.x < this.r) {
            this.p.x = this.r;
            this.vx *= -1;
        } else if (this.p.x > renderer.width-this.r) {
            this.p.x = renderer.width-this.r;
            this.vx *= -1;
        }
        if (this.p.y < this.r) {
            this.p.y = this.r;
            this.vy *= -1;
        } else if (this.p.y > renderer.height-this.r) {
            this.p.y = renderer.height-this.r;
            this.vy *= -1;
        }
    }

    display(){
        renderer.stroke(false);
        renderer.fill(this.color);
        renderer.circle(this);
    }

    bounds(){
      return this;
    }
}

let agents = [];
for (let i = 0; i < 25; i++) agents[i] = new Agent();

// make canvas html element
const cnv = document.getElementById('c2');
// get canvas context
const ctx = cnv.getContext (`2d`)
const chars = "¶Ñ@%&∆∑∫#Wß¥$£√?!†§ºªµ¢çø∂æåπ*™≤≥≈∞~,.…_¬“‘˚`˙"

const div = document.getElementById (`ascii_div`)
div.style.fontFamily = `monospace`
div.style.textAlign = `center`


let quadTree = new c2.QuadTree(new c2.Rect(0,0,renderer.width,renderer.height), 1);

function drawQuadTree(quadTree){
    renderer.stroke('#333333');
    renderer.lineWidth(1);
    renderer.fill(false);
    renderer.rect(quadTree.bounds);

    if(quadTree.leaf()) return;
    for(let i=0; i<4; i++) drawQuadTree(quadTree.children[i]);
}

let circle = new c2.Circle(0, 0, renderer.width/10);


renderer.draw(async () => {
    renderer.clear();

    quadTree.clear();
    quadTree.insert(agents);

    drawQuadTree(quadTree);


    for (let i = 0; i < agents.length; i++) {
        agents[i].update();
        agents[i].display();
    }


    let mouse = new c2.Point(renderer.mouse.x, renderer.mouse.y);
    circle.p = mouse;

    renderer.stroke('#000000');
    renderer.lineWidth(1);
    renderer.lineDash([5, 5]);
    renderer.fill(false);
    renderer.circle(circle);
    renderer.lineDash(false);

    let objects = quadTree.query(circle);

    for(let i=0; i<objects.length; i++){
        renderer.stroke('#000000');
        renderer.lineWidth(1);
        renderer.fill(false);
        renderer.circle(objects[i]);
    }

    // Get image data from context
    const w = renderer.canvas.width
    const h = renderer.canvas.height
    const pixels = renderer.context.getImageData (0, 0, w, h).data

    let ascii_img = ``

    // Match chars with pixel brightness
    for (let y = 0; y < renderer.canvas.height; y += 22) {
        for (let x = 0; x < renderer.canvas.width; x += 10) {
            const i = (y * renderer.canvas.width + x) * 4
            const r = pixels[i]
            const g = pixels[i + 1]
            const b = pixels[i + 2]
            const br = (r * g * b / 16581376) ** 0.1
            const char_i = Math.floor (br * chars.length)
            ascii_img += chars[char_i]
        }
        ascii_img += `\n`
    }
    // Draw ascii on the div
    div.innerText = ascii_img
});


window.addEventListener('resize', resize);
function resize() {
    let parent = renderer.canvas.parentElement;
    renderer.size(parent.clientWidth, parent.clientWidth / 16 * 9);
}
</script>

### Thought Process

When drawing the quad tree and circles in each frame, I can get image data from canvas using `getImageData`. Each pixel has its own data, I can match ascii chars with the pixel brightness, then adding the char to the ascii_img string. Looping into each pixel and find out its matching ascii data, in the end, there will be a long string represents the ascii chars matching the pixels for the whole drawing from canvas. Displaying the ascii string in the "ascii_div" div, we can see the result of combined ideas.

```js
const renderer = new c2.Renderer(document.getElementById("c2"));
resize();

renderer.background("#cccccc");
let random = new c2.Random();

class Agent extends c2.Circle {
  constructor() {
    let x = random.next(renderer.width);
    let y = random.next(renderer.height);
    let r = random.next(10, renderer.width / 15);
    super(x, y, r);

    this.vx = random.next(-2, 2);
    this.vy = random.next(-2, 2);
    this.color = c2.Color.hsl(
      random.next(0, 30),
      random.next(30, 60),
      random.next(20, 100)
    );
  }

  update() {
    this.p.x += this.vx;
    this.p.y += this.vy;

    if (this.p.x < this.r) {
      this.p.x = this.r;
      this.vx *= -1;
    } else if (this.p.x > renderer.width - this.r) {
      this.p.x = renderer.width - this.r;
      this.vx *= -1;
    }
    if (this.p.y < this.r) {
      this.p.y = this.r;
      this.vy *= -1;
    } else if (this.p.y > renderer.height - this.r) {
      this.p.y = renderer.height - this.r;
      this.vy *= -1;
    }
  }

  display() {
    renderer.stroke(false);
    renderer.fill(this.color);
    renderer.circle(this);
  }

  bounds() {
    return this;
  }
}

let agents = [];
for (let i = 0; i < 25; i++) agents[i] = new Agent();

// make canvas html element
const cnv = document.getElementById("c2");
// get canvas context
const ctx = cnv.getContext(`2d`);
const chars = "¶Ñ@%&∆∑∫#Wß¥$£√?!†§ºªµ¢çø∂æåπ*™≤≥≈∞~,.…_¬“‘˚`˙";

const div = document.getElementById(`ascii_div`);
div.style.fontFamily = `monospace`;
div.style.textAlign = `center`;

let quadTree = new c2.QuadTree(
  new c2.Rect(0, 0, renderer.width, renderer.height),
  1
);

function drawQuadTree(quadTree) {
  renderer.stroke("#333333");
  renderer.lineWidth(1);
  renderer.fill(false);
  renderer.rect(quadTree.bounds);

  if (quadTree.leaf()) return;
  for (let i = 0; i < 4; i++) drawQuadTree(quadTree.children[i]);
}

let circle = new c2.Circle(0, 0, renderer.width / 10);

renderer.draw(async () => {
  renderer.clear();

  quadTree.clear();
  quadTree.insert(agents);

  drawQuadTree(quadTree);

  for (let i = 0; i < agents.length; i++) {
    agents[i].update();
    agents[i].display();
  }

  let mouse = new c2.Point(renderer.mouse.x, renderer.mouse.y);
  circle.p = mouse;

  renderer.stroke("#000000");
  renderer.lineWidth(1);
  renderer.lineDash([5, 5]);
  renderer.fill(false);
  renderer.circle(circle);
  renderer.lineDash(false);

  let objects = quadTree.query(circle);

  for (let i = 0; i < objects.length; i++) {
    renderer.stroke("#000000");
    renderer.lineWidth(1);
    renderer.fill(false);
    renderer.circle(objects[i]);
  }

  // Get image data from context
  const w = renderer.canvas.width;
  const h = renderer.canvas.height;
  const pixels = renderer.context.getImageData(0, 0, w, h).data;

  let ascii_img = ``;

  // Match chars with pixel brightness
  for (let y = 0; y < renderer.canvas.height; y += 22) {
    for (let x = 0; x < renderer.canvas.width; x += 10) {
      const i = (y * renderer.canvas.width + x) * 4;
      const r = pixels[i];
      const g = pixels[i + 1];
      const b = pixels[i + 2];
      const br = ((r * g * b) / 16581376) ** 0.1;
      const char_i = Math.floor(br * chars.length);
      ascii_img += chars[char_i];
    }
    ascii_img += `\n`;
  }
  // Draw ascii on the div
  div.innerText = ascii_img;
});

window.addEventListener("resize", resize);
function resize() {
  let parent = renderer.canvas.parentElement;
  renderer.size(parent.clientWidth, (parent.clientWidth / 16) * 9);
}
```

## Why does combining ideas / libraries seem to make things more aesthetically chaotic?

Each idea or library typically comes with its own set of principles, styles, and features, and it creates some extent of effective complexity. When combining elements from multiple ideas or libraries, they can create a sense of visual and conceptual dissonance, resulting in aesthetically chaos. Combining ideas or libraries can also introduce effective complexity by expanding the range of possibilities and interactions within the artwork. This increased effective complexity can lead to a proliferation of visual elements, interactions, and effects, further contributing to the chaotic aesthetic.
