---
title: Week 12 - Assignment 3
published_at: 2024-06-06
snippet: Final thoughts for Assignment 3
disable_html_sanitization: true
---

## Creative Process for Assignment 3

For Assignment 3, my goal is to create a dynamic sharing platform tailored for photographers, whether they are professionals or beginners. It should allows users to upload their best photos along with key camera settings such as ISO, lens focal length, aperture (f-number), and shutter speed. This feature aims to provide a learning resource for photographers to understand how specific settings influence the outcome of a photo.

#### Inspiration

The idea stems from my personal journey in photography. As a beginner, I found it challenging to pinpoint the optimal camera settings for different shots. There were limited platforms where photographers could discuss and share detailed camera settings. I had to keep checking with scouring forums and YouTube for guidance, attempting to replicate the camera settings to capture a stunning shot. This struggle has inspired me to develop a platform where photographers can share their work along with the precise settings used, fostering a community of shared knowledge and growth.

#### Development

To bring this vision to life, I used HTML and CSS to build the homepage. Each photo uploaded by users is presented as a card, with a small tag displaying the relevant camera settings. To enhance the visual and interaction, I added hover effects for the cards using CSS. Additionally, I utilised JavaScript to implement the upload function and enable users to input their camera settings, which located in the `script.js`file.

Drawing inspiration from [Jared Sprague's p5 zoom image example](https://jaredsprague.com/experiments/p5/zoom-image/), I decided to integrate a photo zoom-in feature using p5.js. This allows users to select a photo, view it in a larger dimension, and zoom in on specific areas to appreciate the details and understand how the photo was captured. The code i located in the `photoZoom.js`.

#### Goal

This platform aims to create a rewarding experience by allowing photographers to showcase their work and learn from each other. The hands-on learning experience demonstrates how varying camera settings can dramatically alter the effects of a photo, even when taken at the same location or of the same subject. This project merges my passion for photography with a desire to assist others on their photographic journey. I hope this platform proves as beneficial to others as it has been for me.

## Areas for improvement

Looking ahead, there are several areas where I aim to enhance the platform:

- <b>User Interface</b>: I plan to refine the UI and potentially integrate pagination to facilitate easier browsing as the number of uploaded photos grows.
- <b>Deployment Issues</b>: Currently, the upload function operates perfectly on a local machine (http://localhost:8000) but encounters issues on Deno Deploy due to required write permissions. To resolve this, I am considering alternative deployment methods that would allow the upload function to work correctly on the public website.

I look forward to addressing this issues in the near future, to create a more robust and user-friendly platform, engaging a wider community of photographers.

---
