---
title: Glitch example code comment,
published_at: 2024-04-17
snippet: class example
disable_html_sanitization: true
---

<canvas id="glitch_self_portrait"></canvas>

<script type="module">
    // getting canvas element
   const cnv = document.getElementById (`glitch_self_portrait`)

    // sizing to be good size
   cnv.width = cnv.parentNode.scrollWidth
   cnv.height = cnv.width * 9 / 16

   // setting background colour
   cnv.style.backgroundColor = `deeppink`

    // getting canvas context
   const ctx = cnv.getContext (`2d`)


   // instatiating variable for image data
   let img_data

   // defining a function that draws an image to the canvas
   const draw = i => ctx.drawImage (i, 0, 0, cnv.width, cnv.height)

   // craeating a new image element
   const img = new Image ()

   // defining function to execute upon loading image file
   img.onload = () => {

    // resizing the hight of the canvas
    // to be same aspect ratio as image
      cnv.height = cnv.width * (img.height / img.width)

      // drawing the image to the canvas
      draw (img)

      // storing image data as string in img_data
      img_data = cnv.toDataURL ("image/jpeg")

      // call the glitch function
      add_glitch ()
   }

   //give filepath to image element
   img.src = `/sunset.jpg`
    
   // define a function that a returns a random value between 0 - max
   const rand_int = max => Math.floor (Math.random () * max)

   // define s recursive function 
   const glitchify = (data, chunk_max, repeats) => {

      // random multiple of 4 between 0 - chunk_max
      const chunk_size = rand_int (chunk_max / 4) * 4

      // random position in the data between 24 - chunk_size
      const i = rand_int (data.length - 24 - chunk_size) + 24

      // geabbing all the data before the random position
      const front = data.slice (0, i)

      // leaving a gap the size of the chunk_size
      // grabbing the rest of the data 
      const back = data.slice (i + chunk_size, data.length)

      // putting the two pieces back together
      // leaving out a chunk
      const result = front + back

      // ternary operator to return the reslut if repeats = 0 
      // otherwise call itself again with eapeats -1
      return repeats == 0 ? result : glitchify (result, chunk_max, repeats - 1)
   }

   // instantiate empty array for glitched image
   const glitch_arr = []

   // define function that add s glitch image 
   // to the glitch_arr array
   const add_glitch = () => {

      // make new image element
      const i = new Image ()

      // define function that executes when image recieves its date
      i.onload = () => {

        // push the image into the glitch_arr array
         glitch_arr.push (i)

         // call itself until there are 12 glicthed image
         if (glitch_arr.length < 12) add_glitch ()

         // once there 12 images, start animating
         else draw_frame ()
      }

      // giev the new image some glitchified image data
      i.src = glitchify (img_data, 96, 6)
   }

   // instanting variable to keep track of glitch state
   let is_glitching = false

   // keep track of which glitched image from the array we are using 
   let glitch_i = 0

   const draw_frame = () => {
      
      // check to see if we are glitching
      // if so, fraw the glitched image from the array
      if (is_glitching) draw (glitch_arr[glitch_i])

      // otherwise draw the regular image
      else draw (img)


     // probability weightings for starting and stopping the glitch
      const prob = is_glitching ? 0.05 : 0.02

      // if random value is less than weight value
      if (Math.random () < prob) {

        // choose a random glitched image index
         glitch_i = rand_int (glitch_arr.length)

         // flip the state of is_glitching 
         is_glitching = !is_glitching
      }
   // call the next aninmation 
      requestAnimationFrame (draw_frame)
   }

</script>

```html
<canvas id="glitch_self_portrait"></canvas>

<script type="module">
  // getting canvas element
  const cnv = document.getElementById(`glitch_self_portrait`);

  // sizing to be good size
  cnv.width = cnv.parentNode.scrollWidth;
  cnv.height = (cnv.width * 9) / 16;

  // setting background colour
  cnv.style.backgroundColor = `deeppink`;

  // getting canvas context
  const ctx = cnv.getContext(`2d`);

  // instatiating variablr for image data
  let img_data;

  // defining a function that draws an image to the canvas
  const draw = (i) => ctx.drawImage(i, 0, 0, cnv.width, cnv.height);

  // craeating a new image element
  const img = new Image();

  // defining function to execute upon loading image file
  img.onload = () => {
    // resizing the hight of the canvas
    // to be same aspect ratio as image
    cnv.height = cnv.width * (img.height / img.width);

    // drawing the image to the canvas
    draw(img);

    // storing image data as string in img_data
    img_data = cnv.toDataURL("image/jpeg");

    // call the glitch function
    add_glitch();
  };

  // give filepath to image element
  img.src = `/sunset.jpg`;

  // define a function that a returns a random value between 0 - max
  const rand_int = (max) => Math.floor(Math.random() * max);

  // define s recursive function
  const glitchify = (data, chunk_max, repeats) => {
    // random multiple of 4 between 0 - chunk_max
    const chunk_size = rand_int(chunk_max / 4) * 4;

    // random position in the data between 24 - chunk_size
    const i = rand_int(data.length - 24 - chunk_size) + 24;

    // geabbing all the data before the random position
    const front = data.slice(0, i);

    // leaving a gap the size of the chunk_size
    // grabbing the rest of the data
    const back = data.slice(i + chunk_size, data.length);

    // putting the two pieces back together
    // leaving out a chunk
    const result = front + back;

    // ternary operator to return the reslut if repeats = 0
    // otherwise call itself again with eapeats -1
    return repeats == 0 ? result : glitchify(result, chunk_max, repeats - 1);
  };

  // instantiate empty array for glitched image
  const glitch_arr = [];

  // define functionn that add s glitch image
  // to the glitch_arr array
  const add_glitch = () => {
    // make new image element
    const i = new Image();

    // define function that executes when image recieves its date
    i.onload = () => {
      // push the image into the glitch_arr array
      glitch_arr.push(i);

      // call itself until there are 12 glicthed image
      if (glitch_arr.length < 12) add_glitch();
      // once there 12 images, start animating
      else draw_frame();
    };

    // giev the new image some glitchified image data
    i.src = glitchify(img_data, 96, 6);
  };

  // instanting variable to keep track of glitch state
  let is_glitching = false;

  // keep track of which glitched image from the array we are using
  let glitch_i = 0;

  const draw_frame = () => {
    // check to see if we are glitching
    // if so, fraw the glitched image from the array
    if (is_glitching) draw(glitch_arr[glitch_i]);
    // otherwise draw the regular image
    else draw(img);

    // probability weightings for starting and stopping the glitch
    const prob = is_glitching ? 0.05 : 0.02;

    // if random value is less than weight value
    if (Math.random() < prob) {
      // choose a random glitched image index
      glitch_i = rand_int(glitch_arr.length);

      // flip the state of is_glitching
      is_glitching = !is_glitching;
    }
    // call the next aninmation
    requestAnimationFrame(draw_frame);
  };
</script>
```
