---
title: Week 8 - Assignment 2
published_at: 2024-05-05
snippet: Discuss assignment 2 ideas
disable_html_sanitization: true
---

## which artists / particular works your submission responds

<img title="lit in glitch" alt="glitch" src="lit-in-glitch.png">

[Glitch Design](https://www.sabatobox.com/glitch-design-new-media)

In this assignment, My inspiration came from "Lit in Glitch,” the work of “Glitch Design” from “Sabata Visconti” where they use text as a source for glitch, reimagining important works of literature as animated visualizations. Glitch refers to errors or anomalies in calculators or digital media. Utilizing glitch as a means of expression, this series explores how a seemingly innocuous remark in everyday conversation can inadvertently bring about feelings of sadness or undermine someone's confidence. Through this artwork, I aim to convey the importance of approaching others with positivity and optimism in our lives. A casual remark may have a significant impact on someone else, and I want to emphasize the power of offering encouragement and support to others.

## what techniques you used to achieve a zany / chaotic aesthetic

In my artwork, I aim to create a sense of chaos by employing flickering text effects and moving image blocks.

### 1.Flickering Text

My idea involves displaying a total of five sentences. The first four sentences are encouraging and positive, while the last sentence is discouraging or disheartening.

I create a `GlitchMessage` class and with two main function `setup`, `startGlitch`

```js
class GlitchMessage {
  constructor(message, elementId) {
    this.msg = message;
    this.domElem = document.getElementById(elementId);
    const { mainTextElem, shadowElems } = this.setup();
    this.mainTextElem = mainTextElem;
    this.shadowElems = shadowElems;
  }

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
```

<video controls="controls" width="800" height="600" name="text">
  <source src="text.mov">
</video>

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

To enchance the sense of glitchy effect, I introduce variations in color to the first four sentences.

```js
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
}
```

<video controls="controls" width="800" height="600" name="text2">
  <source src="text2.mov">
</video>

### 2.Image Effect

When the fifth sentence appears, the background image transitions from a smiling face to a saddened expression, while also changing to black and white. Following this, I divide the image into 30\*30 blocks and apply a recursive algorithm to rotate these blocks in a spiral pattern.

<video controls="controls" width="800" height="600" name="text3">
  <source src="text3.mov">
</video>
