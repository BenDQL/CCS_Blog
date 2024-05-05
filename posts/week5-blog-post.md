---
title: Week 5 - Glitch
published_at: 2024-04-10
snippet: Create a self-portrait in glitch style
disable_html_sanitization: true
---

## Explanatory Comments of The Code

Glitch

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

Pixel Sort

```html
<canvas id="pixel_sort"></canvas>

<script type="module">
  // Import PixelSorter class
  import { PixelSorter } from "/scripts/pixel_sort.js";

  // Getting canvas element
  const cnv = document.getElementById(`pixel_sort`);
  // Setting canvas with suitable size
  cnv.width = cnv.parentNode.scrollWidth;
  cnv.height = (cnv.width * 9) / 16;

  // getting canvas context
  const ctx = cnv.getContext(`2d`);
  // Initialise PixelSorter instance
  const sorter = new PixelSorter(ctx);

  // Initialise Image
  const img = new Image();

  img.onload = () => {
    // When image is loaded, setting canvas height with image aspect ratio
    cnv.height = cnv.width * (img.height / img.width);
    // Draw the image on canvas
    ctx.drawImage(img, 0, 0, cnv.width, cnv.height);
    // Setting the sorter size & image_data
    sorter.init();
    // Start the drawing animation
    draw_frame();
  };
  // Setting image source path
  img.src = `/240408/kornerpark.jpg`;

  // Start from the first frame
  let frame_count = 0;
  const draw_frame = () => {
    // Drawing the image on canvas
    ctx.drawImage(img, 0, 0, cnv.width, cnv.height);

    // Calculate the sig value
    let sig = Math.cos((frame_count * 2 * Math.PI) / 500);

    // Getting the middle position on canvas
    const mid = {
      x: cnv.width / 2,
      y: cnv.height / 2,
    };

    // Getting the glitch box ending point position
    const dim = {
      x: Math.floor((sig + 3) * (cnv.width / 6)) + 1,
      y: Math.floor((sig + 1) * (cnv.height / 6)) + 1,
    };

    // Getting the glitch box start point position
    const pos = {
      x: Math.floor(mid.x - dim.x / 2),
      y: Math.floor(mid.y - dim.y / 2),
    };

    // Start glitching
    sorter.glitch(pos, dim);

    // Frame count increases
    frame_count++;
    // Calling requestAnimationFrame for animation
    requestAnimationFrame(draw_frame);
  };
</script>
```

```js
// pixel_sort.js

// Using quicksort to sort array elements in orders
const quicksort = (a) => {
  // If array a doesn't have any elements, such as [], return itself as result
  if (a.length <= 1) return a;

  // Setting the first element value as pivot
  let pivot = a[0];
  // Initialise pivot left & right arrays
  let left = [];
  let right = [];

  for (let i = 1; i < a.length; i++) {
    // Loop into array and compare each element with pivot, if a[i].br is less than pivot.br, put a[i] into left array; otherwise put a[i] into right array.
    // The result is, all the elements with br less than pivot will appear in left array; the larger elements will appear in right array.
    if (a[i].br < pivot.br) left.push(a[i]);
    else right.push(a[i]);
  }

  // Continue on sort the elements into left & right arrays using quick sort using recursion;
  // The end result is that the whole array will be sorted out using quick sort. And the elements will be ordered from small to large with its br value in the array
  const sorted = [...quicksort(left), pivot, ...quicksort(right)];

  // Return the sorted result
  return sorted;
};

export class PixelSorter {
  // Using constructor to initialise PixelSorter with ctx
  constructor(ctx) {
    this.ctx = ctx;
  }

  init() {
    // Setting the size with canvas size; setting the img_data
    this.width = this.ctx.canvas.width;
    this.height = this.ctx.canvas.height;
    this.img_data = this.ctx.getImageData(0, 0, this.width, this.height).data;
  }

  glitch(pos, dim) {
    // For a pixel position {x, y}, find out its pixel data start index in the image data array
    const find_i = (c) => (c.y * this.ctx.canvas.width + c.x) * 4;

    // For each pixel from left to right within the dim box
    for (let x_off = 0; x_off < dim.x; x_off++) {
      const positions = [];
      // Find out each column's pixel data start index within the glitch box, and push it into positions array
      for (let y_pos = pos.y; y_pos < pos.y + dim.y; y_pos++) {
        positions.push(find_i({ x: pos.x + x_off, y: y_pos }));
      }
      // Initilised an unsorted array
      const unsorted = [];
      // Get each pixel data from positions, manipulate its rgb, then push the rgb data into unsorted array
      positions.forEach((p) => {
        const r = this.img_data[p];
        const g = this.img_data[p + 1];
        const b = this.img_data[p + 2];
        const a = this.img_data[p + 3];
        const br = r * g * b;
        unsorted.push({ r, g, b, a, br });
      });
      // Sort the pixel data's opacity from large to small
      const sorted = quicksort(unsorted).reverse();

      let rgba = [];
      // Add back those sorted pixel data into rgba array
      sorted.forEach((e) => {
        rgba.push(e.r);
        rgba.push(e.g);
        rgba.push(e.b);
        rgba.push(e.a);
      });

      rgba = new Uint8ClampedArray(rgba);

      const new_data = this.ctx.createImageData(1, dim.y);
      // Add the manipulated image data as updated image data
      new_data.data.set(rgba);

      // Draw the updated image data on canvas for the glitch box
      this.ctx.putImageData(new_data, pos.x + x_off, pos.y);
    }
  }
}
```

## A Self-Portrait in The Style of Glitch

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
   img.src = `/self-portrait.jpg`
    
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

## Discussion

### Which of Ngai's aesthetic categories does your self-portrait (and glitch more generally) belong to, and why?

I think my self-portrait effect falls into the aesthetic category of "Zany".
This classification captures the essence of unpredictability and exuberant energy, mirroring the erratic nature of your glitched self-portrait. The glitch effect introduces randomness, as the glitched boxes appears with randomly different positions and sizes in each frame, which matches the unpredictability characteristic of "Zany" aesthetics.

### Does glitch increase or decrease effective complexity, and why?

I think glitch aesthetics increases effective complexity. By introducing unpredictability and irregularity, glitches introduce errors and unexpected exceptions that generate chaos, thereby amplifying effective complexity. Additionally, in people's common pereption, complex systems are more likely to break down and are more challenging to fix than simple systems. Introducing glitches can create and enhance audiences' perception of a complex system, further increasing effective complexity.
