---
title: example three.js
published_at: 2024-04-24
snippet: class example
disable_html_sanitization: true
---

<div id="container"></div>

<script type="module">

import * as THREE from '/scripts/three.module.js';
import { ParametricGeometry } from '/scripts/ParametricGeometry.js';
import { OrbitControls } from '/scripts/OrbitControls.js'

console.clear( );

const div = document.getElementById ('container')
const w = div.parentNode.scrollWidth
const h = w * 9 / 16

var scene = new THREE.Scene();
    scene.background = new THREE.Color( 'gainsboro' );

var camera = new THREE.PerspectiveCamera( 30, 16/ 9 );
    camera.position.set( 0, 0, 25 );
    camera.lookAt( scene.position );

var renderer = new THREE.WebGLRenderer( {antialias: true} );
    renderer.setSize( w, h );
    renderer.setAnimationLoop( animationLoop );
    div.appendChild( renderer.domElement );
			
// window.addEventListener( "resize", (event) => {
//     camera.aspect = innerWidth/innerHeight;
//     camera.updateProjectionMatrix( );
//     renderer.setSize( innerWidth, innerHeight );
// });

var controls = new OrbitControls( camera, renderer.domElement );
    controls.enableDamping = true;
		controls.autoRotate = true;

var ambientLight = new THREE.AmbientLight( 'white', 0.5 );
    //scene.add( ambientLight );

var light = new THREE.DirectionalLight( 'white', 3 );
    light.position.set( 1, 1, 1 );
    scene.add( light );


// next comment

function surface( u, v, target )
{
		const n = 10,  // larger values make sharper square
					t = 1.5; // larger values make more twists
	
		u *= 2*Math.PI;
		v *= 2*Math.PI;
	
		var r = (Math.cos(v)**n + Math.sin(v)**n)**(-1/n),
				x = (4+r*Math.cos(v+t*u)) * Math.cos(u),
				y = (4+r*Math.cos(v+t*u)) * Math.sin(u),
				z = r*Math.sin(v+t*u);
	
  	target.set( x, y, z );
}


const geometry = new ParametricGeometry(surface, 100, 100);


var object = new THREE.Mesh(
			geometry,
			new THREE.MeshNormalMaterial(),
    );	
		scene.add( object );



function animationLoop( t )
{
   object.rotation.z = t/2700;

    controls.update( );
		light.position.copy( camera.position );
    renderer.render( scene, camera );
}

</script>
