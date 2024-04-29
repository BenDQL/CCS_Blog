---
title: ascil cam
published_at: 2024-04-24
snippet: class example
disable_html_sanitization: true
---

<div id="ascii_div"></div>

<script type="module">

    //git camare stream, with optiond object
   const stream = await navigator.mediaDevices.getUserMedia ({ 
      audio: false,
      video: true,
      facingMode: `user`,
   })
    
    //get video tracks
   const videoTracks = await stream.getVideoTracks ()
   console.log (`Using video device: ${ videoTracks[0].label }`)

    //make avideo html element
   const video = document.createElement (`video`)

   //asssign stream to video source
   video.srcObject = stream
   await video.play ()

   //make canvas html element
   const cnv = document.createElement (`canvas`)

   //small size
   cnv.width  = 64

   //with aspect raito from video html element
   cnv.height = cnv.width * video.videoHeight / video.videoWidth

   //grab the ascii div from DOM
   const div = document.getElementById (`ascii_div`)

   //set font to be monospace
   div.style.fontFamily = `monospace`

   //center algigned
   div.style.textAlign = `center`

   //get canvas context
   const ctx = cnv.getContext (`2d`)

   // string of characters from dark - bright
   const chars = "¶Ñ@%&∆∑∫#Wß¥$£√?!†§ºªµ¢çø∂æåπ*™≤≥≈∞~,.…_¬“‘˚`˙"

   //defining a function for animation
   const draw_frame = async () => {

      //tranformation save point
      ctx.save ()

      //flip horizontally
      ctx.scale (-1, 1)

      //draw image from video onto wrong side
      ctx.drawImage (video, -cnv.width, 0, cnv.width, cnv.height)

      
      ctx.restore ()

      //get piexl data
      const pixels = await ctx.getImageData (0, 0, cnv.width, cnv.height).data

      //flip string
      let ascii_img = ``

      //
      for (let y = 0; y < cnv.height; y += 2) {
         for (let x = 0; x < cnv.width; x++) {

            //get piexl piston 
            const i = (y * cnv.width + x) * 4

            // get rgb values
            const r = pixels[i]
            const g = pixels[i + 1]
            const b = pixels[i + 2]

            //caluate brightness
            const br = (r * g * b / 16581376) ** 0.1

            // use breghtness to select character
            const char_i = Math.floor (br * chars.length)

            //add character to ascii string
            ascii_img += chars[char_i]
         }
         ascii_img += `\n`
      }

      //add ascii string to innerText of div
      div.innerText = ascii_img

      //wait and them callnext frame
      requestAnimationFrame (draw_frame)
   }

   //satrt 
   draw_frame ()
</script>
