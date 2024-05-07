---
title: Week 8 - Assignment 2
published_at: 2024-05-05
snippet: Discuss assignment 2 ideas
disable_html_sanitization: true
---

## Respond to Sabata Visconti's "Glitch Design" - "Lit in Glitch"

<img title="lit in glitch" alt="glitch" src="lit-in-glitch.png">

[Glitch Design](https://www.sabatobox.com/glitch-design-new-media)

In this assignment, my inspiration came from Sabata Visconti's "Lit in Glitch” in his "Glitch Design" collection. The artwork used text as a source for glitch, to reimagine important works of literature as animated visualizations. I'd like to use text as a source of glitching, but I'd like to expand the concept of "text" to be messages or words we hear from others every day. One message from us to other people can affect their entire day. When hearing negative or bad messages, our emotion can be affected or even ruined, just like "glitch" - introducing "errors" or "exceptions" on our mood or our emotion.

By creating this art piece, I aim to highlight the importance of what we say to others. Words have power - a casual negative word we've just said might have a significant impact on someone else, thereby always thinking before saying.

## Techniques Used to Achieve A Zany / Chaotic Aesthetic

In my artwork, I tried to create chaos by combining flickering text effects and small image blocks moving in spiral.

### 1.Flickering Text

My idea involves displaying 5 sentences. The first four sentences are randomly encouraging and positive messages, while the last sentence is discouraging or disheartening. When each sentence is displayed, it comes with flickering effect.

I create a `GlitchMessage` class with a main function `startGlitch`.

```js
class GlitchMessage {
  constructor(message, elementId) {
    this.msg = message;
    this.domElem = document.getElementById(elementId);
    const { mainTextElem, shadowElems } = this.setup();
    this.mainTextElem = mainTextElem;
    this.shadowElems = shadowElems;
  }

  startGlitch = () => {
    // Start the glitched text effect
    this.mainTextElem.classList.add("glitched");
    this.shadowElems.forEach((shadowElem) => {
      shadowElem.classList.add("glitched");
    });
    setTimeout(() => {
      this.mainTextElem.classList.remove("glitched");
      this.shadowElems.forEach((shadowElem) =>
        shadowElem.classList.remove("glitched")
      );
    }, 50 + randomInt(100));
  };
```

Then I added positive & negative messages to display in order.

```js
const [goodMessages, badMessages] = [
  [
    "You look amazing today!",
    "Thanks for everything you do.",
    "You've done a great job!",
    "We're so lucky to have you.",
    "You make a difference in my life.",
    "Anytime you need help, I'm here for you.",
    "I trust you.",
    "I love the way you present.",
    "You're very good at that!",
  ],
  [
    "You're a loser!",
    "You're such a stupid person!",
    "You never get anything right.",
    "You're hopeless!",
    "I wish you never born.",
  ],
];
const startMessageAnimation = (i, cb) => {
  let displayText;
  if (i < 4) {
    // Display good message for 4 times
    displayText = randomReplaceText(true, goodMessages, badMessages);
  } else if (i === 4) {
    // Display bad message on the 5-th time
    displayText = randomReplaceText(false, goodMessages, badMessages);
  } else {
    return cb();
  }
  document.getElementById("glitch-text").innerHTML = "";
  const glitchedMsg = new GlitchMessage(displayText, "glitch-text");
  glitchedMsg.startGlitch();
  // Replace with new message after some time
  setTimeout(() => startMessageAnimation(i + 1, cb), 350 + randomInt(500));
};
```

<video controls="controls" width="800" height="600" name="text">
  <source src="text.mov">
</video>

<p></p>

To enchance the sense of glitchy effect, I introduce variations in color red and blue to for the flickering effect when the text is shown - logic is in the `setup` function:

```js
class GlitchMessage {
  // ...
  setup = () => {
    // Add child div for displaying main text and its shadow layer for glitched effect
    const mainTextElem = document.createElement("div");
    mainTextElem.classList.add("main-text");
    mainTextElem.textContent = this.msg;
    this.domElem.appendChild(mainTextElem);

    const shadowElem1 = document.createElement("div");
    shadowElem1.classList.add("shadow");
    shadowElem1.textContent = this.msg;
    this.domElem.appendChild(shadowElem1);

    const shadowElem2 = document.createElement("div");
    shadowElem2.classList.add("shadow");
    shadowElem2.classList.add("shawdow-red");
    shadowElem2.textContent = this.msg;
    this.domElem.appendChild(shadowElem2);

    const shadowElems = [shadowElem1, shadowElem2];

    return { mainTextElem, shadowElems };
  };
}
```

<video controls="controls" width="800" height="600" name="text2">
  <source src="text2.mov">
</video>

### 2.Image Effect

When the fifth sentence appears, the background image transitions from a smiling face to a saddened expression, and the image will be grayed to reflect emotion has been "ruined".

I created a class called `ImageGlitch`. Then, I divided the image into 30\*30 small blocks and used a recursive function to move these blocks in a spiral pattern. `requestAnimationFrame` is used for updating the animation each frame.

```js
/* Image Glitch */
class ImageGlitch {
  constructor(path1, path2, cnv) {
    // Initialise happy image
    const happyImg = new Image();
    happyImg.crossOrigin = "anonymous";
    happyImg.src = path1;
    this.happyImg = happyImg;
    this.img = happyImg;
    this.img.onload = () => {
      this.initDrawImage(this.img);
    };
    // Initialise happy image
    const sadImg = new Image();
    sadImg.crossOrigin = "anonymous";
    sadImg.src = path2;
    this.sadImg = sadImg;

    // Canvas & context
    this.cnv = cnv;
    this.ctx = cnv.getContext("2d");
    this.frameId = null;
    this.blocksArray = [];
  }

  setup = (amount) => {
    for (let i = 0; i < amount; i = i + 2) {
      for (let j = 0; j < amount; j = j + 2) {
        const edge = Math.floor(500 / amount);
        const startX = i * edge;
        const startY = j * edge;
        const block = new Block(edge, startX, startY, this, this.cnv);
        this.blocksArray.push(block);
      }
    }
  };

  drawImg = (sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight) =>
    this.ctx.drawImage(
      this.img,
      sx,
      sy,
      sWidth,
      sHeight,
      dx,
      dy,
      dWidth,
      dHeight
    );

  initDrawImage = () => {
    this.drawImg(
      0,
      0,
      this.img.width,
      this.img.height,
      (this.cnv.width - this.img.width) / 2,
      (this.cnv.height - this.img.height) / 2,
      this.img.width,
      this.img.height
    );
  };

  // Make the image gray
  grayscaleEffect = () => {
    this.img = this.sadImg;
    this.ctx.filter = "grayscale(1)";
    this.initDrawImage();
  };

  movingBlocks = (i) => {
    // Recursion
    this.blocksArray.forEach((block) => {
      const { updatedX, updatedY } = block.moveInSpiral(Math.floor(i));
      const canvasX = (this.cnv.width - this.img.width) / 2;
      const canvasY = (this.cnv.height - this.img.height) / 2;
      this.drawImg(
        block.startX,
        block.startY,
        block.edge,
        block.edge,
        canvasX + updatedX,
        canvasY + updatedY,
        block.edge,
        block.edge
      );
    });
    this.frameId = requestAnimationFrame(() => this.movingBlocks(i + 0.5));
  };

  stopGlitch = () => {
    cancelAnimationFrame(this.frameId);
    this.frameId = null;
    this.ctx.clearRect(0, 0, canvas.width, canvas.height);
    this.img = this.happyImg;
    this.ctx.filter = "none";
    this.initDrawImage();
    this.blocksArray = [];
  };
}
```

I also have a `Block` class to represent each small block, and it has `moveInSpiral` function to calculate its next position {x, y} as it moves in spiral each frame

```js
/* Block - Divide image into blocks */
class Block {
  constructor(edge, startX, startY) {
    this.edge = edge;
    this.startX = startX;
    this.startY = startY;
    this.angleRatio = randomRatio(0.2, 0.1);
  }

  // Generate the {x, y} to let block move in spiral with speed ratio
  moveInSpiral = (i) => {
    const a = 5,
      b = 5;
    const angle = this.angleRatio * i;
    const updatedX = this.startX + (a + b * angle) * Math.cos(angle);
    const updatedY = this.startY + (a + b * angle) * Math.sin(angle);

    return { updatedX, updatedY };
  };
}
```

<video controls="controls" width="800" height="600" name="text3">
  <source src="text3.mov">
</video>

---
