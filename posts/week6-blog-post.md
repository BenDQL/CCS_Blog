---
title: Week 6 - The Post Digital
published_at: 2024-04-17
snippet: c2.js & three.js example
disable_html_sanitization: true
---

## c2.js example

<canvas id="c2"></canvas>

<script src="/scripts/c2.js"></script>
<!-- <script scr="/static/scripts/p5.js"></script> -->

<script>


    let canvas = document.getElementById('c2');
    let renderer = new c2.Renderer(canvas);

   // const renderer = new Renderer(document.getElementById('c2'));
    resize();

    renderer.background('#cccccc');
    let random = new c2.Random();


    class Agent extends c2.Point {
        constructor() {
            let x = random.next(renderer.width);
            let y = random.next(renderer.height);
            super(x, y);

            this.vx = random.next(-2, 2);
            this.vy = random.next(-2, 2);
        }

        update() {
            this.x += this.vx;
            this.y += this.vy;

            if (this.x < 0) {
                this.x = 0;
                this.vx *= -1;
            } else if (this.x > renderer.width) {
                this.x = renderer.width;
                this.vx *= -1;
            }
            if (this.y < 0) {
                this.y = 0;
                this.vy *= -1;
            } else if (this.y > renderer.height) {
                this.y = renderer.height;
                this.vy *= -1;
            }
        }

        display() {
            renderer.stroke('#333333');
            renderer.lineWidth(5);
            renderer.point(this.x, this.y);
        }
    }

    let agents = new Array(20);
    for (let i = 0; i < agents.length; i++) agents[i] = new Agent();


    renderer.draw(() => {
        renderer.clear();

        let delaunay = new c2.Delaunay();
        delaunay.compute(agents);
        let vertices = delaunay.vertices;
        let edges = delaunay.edges;
        let triangles = delaunay.triangles;

        let maxArea = 0;
        let minArea = Number.POSITIVE_INFINITY;
        for (let i = 0; i < triangles.length; i++) {
            let area = triangles[i].area();
            if(area < minArea) minArea = area;
            if(area > maxArea) maxArea = area;
        }

        renderer.stroke('#333333');
        renderer.lineWidth(1);
        for (let i = 0; i < triangles.length; i++) {
            let t = c2.norm(triangles[i].area(), minArea, maxArea);
            let color = c2.Color.hsl(30*t, 30+30*t, 20+80*t);
            renderer.fill(color);
            renderer.triangle(triangles[i]);
        }
        

        for (let i = 0; i < agents.length; i++) {
            agents[i].display();
            agents[i].update();
        }
    });


    window.addEventListener('resize', resize);
    function resize() {
        let parent = renderer.canvas.parentElement;
        renderer.size(parent.clientWidth, parent.clientWidth / 16 * 9);
    }
</script>

```html
<canvas id="c2"></canvas>

<script src="/scripts/c2.js"></script>
<!-- <script scr="/static/scripts/p5.js"></script> -->

<script>
  let canvas = document.getElementById("c2");
  let renderer = new c2.Renderer(canvas);

  // const renderer = new Renderer(document.getElementById('c2'));
  resize();

  renderer.background("#cccccc");
  let random = new c2.Random();

  class Agent extends c2.Point {
    constructor() {
      let x = random.next(renderer.width);
      let y = random.next(renderer.height);
      super(x, y);

      this.vx = random.next(-2, 2);
      this.vy = random.next(-2, 2);
    }

    update() {
      this.x += this.vx;
      this.y += this.vy;

      if (this.x < 0) {
        this.x = 0;
        this.vx *= -1;
      } else if (this.x > renderer.width) {
        this.x = renderer.width;
        this.vx *= -1;
      }
      if (this.y < 0) {
        this.y = 0;
        this.vy *= -1;
      } else if (this.y > renderer.height) {
        this.y = renderer.height;
        this.vy *= -1;
      }
    }

    display() {
      renderer.stroke("#333333");
      renderer.lineWidth(5);
      renderer.point(this.x, this.y);
    }
  }

  let agents = new Array(20);
  for (let i = 0; i < agents.length; i++) agents[i] = new Agent();

  renderer.draw(() => {
    renderer.clear();

    let delaunay = new c2.Delaunay();
    delaunay.compute(agents);
    let vertices = delaunay.vertices;
    let edges = delaunay.edges;
    let triangles = delaunay.triangles;

    let maxArea = 0;
    let minArea = Number.POSITIVE_INFINITY;
    for (let i = 0; i < triangles.length; i++) {
      let area = triangles[i].area();
      if (area < minArea) minArea = area;
      if (area > maxArea) maxArea = area;
    }

    renderer.stroke("#333333");
    renderer.lineWidth(1);
    for (let i = 0; i < triangles.length; i++) {
      let t = c2.norm(triangles[i].area(), minArea, maxArea);
      let color = c2.Color.hsl(30 * t, 30 + 30 * t, 20 + 80 * t);
      renderer.fill(color);
      renderer.triangle(triangles[i]);
    }

    for (let i = 0; i < agents.length; i++) {
      agents[i].display();
      agents[i].update();
    }
  });

  window.addEventListener("resize", resize);
  function resize() {
    let parent = renderer.canvas.parentElement;
    renderer.size(parent.clientWidth, (parent.clientWidth / 16) * 9);
  }
</script>
```

## three.js

Code heavily inspired by [this codepen](https://codepen.io/prisoner849/pen/ExpLBEO)

 <div id="three_container"></div>

<script type="module">
  import * as THREE from "/scripts/three.module.js";
  import { OrbitControls } from "/scripts/OrbitControls.js";

  let noise = `//	Simplex 4D Noise 
//	by Ian McEwan, Ashima Arts
//
vec4 permute(vec4 x){return mod(((x*34.0)+1.0)*x, 289.0);}
float permute(float x){return floor(mod(((x*34.0)+1.0)*x, 289.0));}
vec4 taylorInvSqrt(vec4 r){return 1.79284291400159 - 0.85373472095314 * r;}
float taylorInvSqrt(float r){return 1.79284291400159 - 0.85373472095314 * r;}

vec4 grad4(float j, vec4 ip){
  const vec4 ones = vec4(1.0, 1.0, 1.0, -1.0);
  vec4 p,s;

  p.xyz = floor( fract (vec3(j) * ip.xyz) * 7.0) * ip.z - 1.0;
  p.w = 1.5 - dot(abs(p.xyz), ones.xyz);
  s = vec4(lessThan(p, vec4(0.0)));
  p.xyz = p.xyz + (s.xyz*2.0 - 1.0) * s.www; 

  return p;
}

float snoise(vec4 v){
  const vec2  C = vec2( 0.138196601125010504,  // (5 - sqrt(5))/20  G4
                        0.309016994374947451); // (sqrt(5) - 1)/4   F4
// First corner
  vec4 i  = floor(v + dot(v, C.yyyy) );
  vec4 x0 = v -   i + dot(i, C.xxxx);

// Other corners

// Rank sorting originally contributed by Bill Licea-Kane, AMD (formerly ATI)
  vec4 i0;

  vec3 isX = step( x0.yzw, x0.xxx );
  vec3 isYZ = step( x0.zww, x0.yyz );
//  i0.x = dot( isX, vec3( 1.0 ) );
  i0.x = isX.x + isX.y + isX.z;
  i0.yzw = 1.0 - isX;

//  i0.y += dot( isYZ.xy, vec2( 1.0 ) );
  i0.y += isYZ.x + isYZ.y;
  i0.zw += 1.0 - isYZ.xy;

  i0.z += isYZ.z;
  i0.w += 1.0 - isYZ.z;

  // i0 now contains the unique values 0,1,2,3 in each channel
  vec4 i3 = clamp( i0, 0.0, 1.0 );
  vec4 i2 = clamp( i0-1.0, 0.0, 1.0 );
  vec4 i1 = clamp( i0-2.0, 0.0, 1.0 );

  //  x0 = x0 - 0.0 + 0.0 * C 
  vec4 x1 = x0 - i1 + 1.0 * C.xxxx;
  vec4 x2 = x0 - i2 + 2.0 * C.xxxx;
  vec4 x3 = x0 - i3 + 3.0 * C.xxxx;
  vec4 x4 = x0 - 1.0 + 4.0 * C.xxxx;

// Permutations
  i = mod(i, 289.0); 
  float j0 = permute( permute( permute( permute(i.w) + i.z) + i.y) + i.x);
  vec4 j1 = permute( permute( permute( permute (
             i.w + vec4(i1.w, i2.w, i3.w, 1.0 ))
           + i.z + vec4(i1.z, i2.z, i3.z, 1.0 ))
           + i.y + vec4(i1.y, i2.y, i3.y, 1.0 ))
           + i.x + vec4(i1.x, i2.x, i3.x, 1.0 ));
// Gradients
// ( 7*7*6 points uniformly over a cube, mapped onto a 4-octahedron.)
// 7*7*6 = 294, which is close to the ring size 17*17 = 289.

  vec4 ip = vec4(1.0/294.0, 1.0/49.0, 1.0/7.0, 0.0) ;

  vec4 p0 = grad4(j0,   ip);
  vec4 p1 = grad4(j1.x, ip);
  vec4 p2 = grad4(j1.y, ip);
  vec4 p3 = grad4(j1.z, ip);
  vec4 p4 = grad4(j1.w, ip);

// Normalise gradients
  vec4 norm = taylorInvSqrt(vec4(dot(p0,p0), dot(p1,p1), dot(p2, p2), dot(p3,p3)));
  p0 *= norm.x;
  p1 *= norm.y;
  p2 *= norm.z;
  p3 *= norm.w;
  p4 *= taylorInvSqrt(dot(p4,p4));

// Mix contributions from the five corners
  vec3 m0 = max(0.6 - vec3(dot(x0,x0), dot(x1,x1), dot(x2,x2)), 0.0);
  vec2 m1 = max(0.6 - vec2(dot(x3,x3), dot(x4,x4)            ), 0.0);
  m0 = m0 * m0;
  m1 = m1 * m1;
  return 49.0 * ( dot(m0*m0, vec3( dot( p0, x0 ), dot( p1, x1 ), dot( p2, x2 )))
               + dot(m1*m1, vec2( dot( p3, x3 ), dot( p4, x4 ) ) ) ) ;

}`;

class Particles extends THREE.Points {
  constructor(gu) {
    let particlePos = []; //vec4 (speed, shift radius, phase, reserved);
    let particleAlpha = []; //vec4 (phase, speed, reserved, reserved)
    let g = new THREE.BufferGeometry().setFromPoints(
      new Array(1000).fill().map((_) => {
        
        particlePos.push(Math.random() * 0.2 + 0.2, Math.random() + 1, Math.random(), 0);
        particleAlpha.push(Math.random(), Math.random() * 0.4 + 0.1, 0, 0);
        
        let r = 5;
        return new THREE.Vector3().setFromCylindricalCoords(
          Math.sqrt(r * r * Math.random()),
          Math.random() * Math.PI * 2,
          Math.random() * 10
        );
      })
    ).setAttribute("particlePos", new THREE.Float32BufferAttribute(particlePos, 4))
    .setAttribute("particleAlpha", new THREE.Float32BufferAttribute(particleAlpha, 4));
    
    let m = new THREE.PointsMaterial({
      size: 0.5,
      color: "white",
      transparent: true,
      depthTest: false,
      onBeforeCompile: (shader) => {
        shader.uniforms.time = gu.time;
        shader.uniforms.heightLimMin = { value: 0 };
        shader.uniforms.heightLimMax = { value: 10 };
        shader.vertexShader = `
          uniform float time;
          uniform float heightLimMin;
          uniform float heightLimMax;
          attribute vec4 particlePos;
          attribute vec4 particleAlpha;
          varying float vParticleAlpha;
          mat2 rot(float a){float c = cos(a); float s = sin(a); return mat2(c, s, -s, c);}
          ${noise}
          ${shader.vertexShader}
        `.replace(
          `#include <begin_vertex>`,
          `#include <begin_vertex>
            float t = time;
          
            vParticleAlpha = sin(mod((particleAlpha.x + t * particleAlpha.y) * PI2, PI2)) * 0.5 + 0.5;
                        
            float hGap = heightLimMax - heightLimMin;
            float halfGap = hGap * 0.5;
            
            float verticalSpeed = particlePos.x;
            float verticalShift = mod((position.y - heightLimMin) + verticalSpeed * t, hGap);
            transformed.y = heightLimMin + verticalShift; // make it looped on Y-axis
            float verticalFade = smoothstep(halfGap, halfGap - 1., abs(verticalShift - halfGap)); // for both top and bottom
            vParticleAlpha *= verticalFade;
            
            // particle shift
            float n = snoise(vec4(position, t * 0.05));
            float radius = particlePos.y;
            float phase = particlePos.z;
            
            float angle = (phase + n) * PI ;
            vec2 shiftVec = rot(angle) * vec2(radius, 0.);
            
            transformed.xz += shiftVec; // make it shifting
            
          `
        ).replace(
          `gl_PointSize = size;`,
          `gl_PointSize = size * vParticleAlpha;`
        );
        //console.log(shader.vertexShader);
        shader.fragmentShader = `
          varying float vParticleAlpha;
          ${shader.fragmentShader}
        `.replace(
          `#include <color_fragment>`,
          `#include <color_fragment>
          vec2 uv = gl_PointCoord.xy - 0.5;
          float d = sqrt(dot(uv, uv));
          if(d > 0.5) discard;
          float f = smoothstep(0.5, 0., d);
          f *= f * f * f * f;
          f = f * 0.95 + 0.05;
          diffuseColor.a = vParticleAlpha * f;
          `
        );
        //console.log(shader.fragmentShader);
      }
    });
    super(g, m);
  }
}

const div = document.getElementById ('three_container')
const w = div.parentNode.scrollWidth
const h = w * 9 / 16

let scene = new THREE.Scene();
scene.background = new THREE.Color("maroon");
let camera = new THREE.PerspectiveCamera(60, 16 /9 , 1, 1000);
camera.position.set(0, 5, 10);
let renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(w, h);
div.appendChild(renderer.domElement);
// window.addEventListener("resize", (event) => {
//   camera.aspect = innerWidth / innerHeight;
//   camera.updateProjectionMatrix();
//   renderer.setSize(innerWidth, innerHeight);
// });

let controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.target.set(0, 5, 0);

let gu = {
  time: { value: 0 }
};

let grid = new THREE.GridHelper();
scene.add(grid);

let particles = new Particles(gu);
scene.add(particles);

let clock = new THREE.Clock();

renderer.setAnimationLoop((_) => {
  let t = clock.getElapsedTime();
  gu.time.value = t;
  controls.update();
  renderer.render(scene, camera);
});
</script>

```js
class Particles extends THREE.Points {
  constructor(gu) {
    let particlePos = []; //vec4 (speed, shift radius, phase, reserved);
    let particleAlpha = []; //vec4 (phase, speed, reserved, reserved)
    let g = new THREE.BufferGeometry()
      .setFromPoints(
        new Array(1000).fill().map((_) => {
          particlePos.push(
            Math.random() * 0.2 + 0.2,
            Math.random() + 1,
            Math.random(),
            0
          );
          particleAlpha.push(Math.random(), Math.random() * 0.4 + 0.1, 0, 0);

          let r = 5;
          return new THREE.Vector3().setFromCylindricalCoords(
            Math.sqrt(r * r * Math.random()),
            Math.random() * Math.PI * 2,
            Math.random() * 10
          );
        })
      )
      .setAttribute(
        "particlePos",
        new THREE.Float32BufferAttribute(particlePos, 4)
      )
      .setAttribute(
        "particleAlpha",
        new THREE.Float32BufferAttribute(particleAlpha, 4)
      );

    let m = new THREE.PointsMaterial({
      size: 0.5,
      color: "white",
      transparent: true,
      depthTest: false,
      onBeforeCompile: (shader) => {
        shader.uniforms.time = gu.time;
        shader.uniforms.heightLimMin = { value: 0 };
        shader.uniforms.heightLimMax = { value: 10 };
        shader.vertexShader = `
          uniform float time;
          uniform float heightLimMin;
          uniform float heightLimMax;
          attribute vec4 particlePos;
          attribute vec4 particleAlpha;
          varying float vParticleAlpha;
          mat2 rot(float a){float c = cos(a); float s = sin(a); return mat2(c, s, -s, c);}
          ${noise}
          ${shader.vertexShader}
        `
          .replace(
            `#include <begin_vertex>`,
            `#include <begin_vertex>
            float t = time;

            vParticleAlpha = sin(mod((particleAlpha.x + t * particleAlpha.y) * PI2, PI2)) * 0.5 + 0.5;

            float hGap = heightLimMax - heightLimMin;
            float halfGap = hGap * 0.5;

            float verticalSpeed = particlePos.x;
            float verticalShift = mod((position.y - heightLimMin) + verticalSpeed * t, hGap);
            transformed.y = heightLimMin + verticalShift; // make it looped on Y-axis
            float verticalFade = smoothstep(halfGap, halfGap - 1., abs(verticalShift - halfGap)); // for both top and bottom
            vParticleAlpha *= verticalFade;

            // particle shift
            float n = snoise(vec4(position, t * 0.05));
            float radius = particlePos.y;
            float phase = particlePos.z;

            float angle = (phase + n) * PI ;
            vec2 shiftVec = rot(angle) * vec2(radius, 0.);

            transformed.xz += shiftVec; // make it shifting

          `
          )
          .replace(
            `gl_PointSize = size;`,
            `gl_PointSize = size * vParticleAlpha;`
          );
        //console.log(shader.vertexShader);
        shader.fragmentShader = `
          varying float vParticleAlpha;
          ${shader.fragmentShader}
        `.replace(
          `#include <color_fragment>`,
          `#include <color_fragment>
          vec2 uv = gl_PointCoord.xy - 0.5;
          float d = sqrt(dot(uv, uv));
          if(d > 0.5) discard;
          float f = smoothstep(0.5, 0., d);
          f *= f * f * f * f;
          f = f * 0.95 + 0.05;
          diffuseColor.a = vParticleAlpha * f;
          `
        );
        //console.log(shader.fragmentShader);
      },
    });
    super(g, m);
  }
}

let scene = new THREE.Scene();
scene.background = new THREE.Color("maroon");
let camera = new THREE.PerspectiveCamera(60, innerWidth / innerHeight, 1, 1000);
camera.position.set(0, 5, 10);
let renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(innerWidth, innerHeight);
div.appendChild(renderer.domElement);
// window.addEventListener("resize", (event) => {
//   camera.aspect = innerWidth / innerHeight;
//   camera.updateProjectionMatrix();
//   renderer.setSize(innerWidth, innerHeight);
// });

let controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.target.set(0, 5, 0);

let gu = {
  time: { value: 0 },
};

let grid = new THREE.GridHelper();
scene.add(grid);

let particles = new Particles(gu);
scene.add(particles);

let clock = new THREE.Clock();

renderer.setAnimationLoop((_) => {
  let t = clock.getElapsedTime();
  gu.time.value = t;
  controls.update();
  renderer.render(scene, camera);
});
```
