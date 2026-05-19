# Computer Graphics Project

Dieses Repository enthält ein interaktives Computer Graphics Projekt, das mit SvelteKit und Threlte (Three.js under the hood) kreiert wurde. Das Projekt ist ein 3D-Modell eines Zimmers mit Retro-Geräten, spezifischer einem Arcade-Automaten und einem Tisch mit Fernseher und einer Art Atari. Im Menu des Web UIs kann man zwischen folgenden Perspektiven wechseln:

- `Whole Room`: zeigt die gesamte Komposition
- `Arcade screen`: zoomt zum Bildschirm des Arcade. Von hier aus kann man die Joysticks kontrollieren

## Lernziele

Das Ziel des Projekt war es, ein in Blender selbst modelliertes low-poly Modell im Web Interaktiv zu machen. Dieses low-poly Modell wurde als GLB-File exportiert und mit Kameras und ein paar Lichtern zum Leben erweckt. Die Joysticks des Arcade-Automaten wurden auch programmatisch animiert. Die ganze Szene wäre rein theoretisch sehr "reusable" und "composable", da die Modelle und Teile der Szene in mehrheitlich separaten Komponenten leben.

## Tech-Stack

Für die Vervollständigung wurden folgende Technologien angewendet.

### Svelte
[Svelte](https://svelte.dev) ist eine Templating-Sprache, die es erlaubt, HTML und TypeScript sehr nah aneinander zu schreiben. SvelteKit stellt ein Meta-Framework dar, das für die Web-Applikations Struktur benutzt wurde, da ich mich da bereits gut auskenne und man sehr einfach eine Web-App entwickeln und auch auf Vercel (https://vercel.com) deployen kann. Svelte benutzt einen Compiler, um sogenannte Svelte-Komponenten, wie diesen (den ich schamlos aus der Dokumentation geklaut habe):
```svelte
<script lang="ts">
	function greet() {
		alert('Welcome to Svelte!');
	}
</script>

<button onclick={greet}>click me</button>

<style>
	button {
		font-size: 2em;
	}
</style>
```

in optimisiertes JavaScript kompiliert.

### Three.js
[Three.js](https://threejs.org) ist eine JavaScript-Library, die viele 3D-primitives für das Web, basierend auf WebGL mitbringt. Man kann mit Three.js 3D Grafiken direkt im Browser darstellen. Man muss also kein WebGL schreiben können, um komplexe Szenen auf einer Website abbilden zu können. Three.js ist bekannt für seinen imperativen JS-Style, was meiner Meinung nach etwas zu kompliziert ist (aber auch durchaus sinn macht, wenn man three.js in einer Vanilla-JS Seite nutzen will), die nächste Library erleichtert mir den Umgang mit Three.js stark und fördert auch die Integration mit Svelte.

### Threlte
[Threlte](https://threlte.xyz) dient als die Verbindungsebene zwischen Svelte und Three.js. Er macht die imperativen Bindings von Three.js deklarativ und reaktiv, was optimal ist, da ich somit Interaktionen und Animationen in meine Szene einbringen kann. Threlte stellt auch die CLI zur Verfügung, mit der ich die `.glb`-Datei von Blender in einen Component umwandeln konnte. Hier ein Beispiel einer Three.js-Szene:
```js
import * as THREE from 'three'

const scene = new THREE.Scene()

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  100
)
camera.position.z = 3

const renderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshBasicMaterial({ color: 'hotpink' })
const cube = new THREE.Mesh(geometry, material)

scene.add(cube)

function animate() {
  cube.rotation.y += 0.01
  renderer.render(scene, camera)
  requestAnimationFrame(animate)
}

animate()
```
, die deklarativ und reaktiv mit Threlte folgendermassen verfasst werden kann:
```svelte
<script>
  import { Canvas, T } from '@threlte/core'
  import { useTask } from '@threlte/core'

  let rotationY = 0

  useTask(() => {
    rotationY += 0.01
  })
</script>

<Canvas>
  <T.PerspectiveCamera position={[0, 0, 3]} />

  <T.Mesh rotation.y={rotationY}>
    <T.BoxGeometry args={[1, 1, 1]} />
    <T.MeshBasicMaterial color="hotpink" />
  </T.Mesh>
</Canvas>
```

## Projectstruktur

- [src/routes/+page.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/routes/+page.svelte): page-level UI und Kamera-Modus Buttons.
- [src/lib/Scene.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Scene.svelte): 3D Szene, sowie die Kamera und Joystick-Logik
- [src/lib/Room.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Room.svelte): Auto-generierter Svelte-Component aus dem GLB Modell
- `static/room-transformed.glb`: optimisertes 3D-Modell, generiert von dem CLI
- `models/room.glb`: exportiertes 3D-Modell aus Blender

## Interaktion

Die Buttons oben rechts dienen dazu, zwischen den zwei Kamera-Modi (oder Perspektiven) zu wechseln. Damit das 3D-Modell angemessen gross ist, muss man im browser unter umständen rein- oder rauszoomen. Wenn man sich im `Arcade screen`-Modus befindet, kann man mit `W`, `A`, `S` und `D` den linken Joystick bedienen und mit den Pfeiltasten den rechten.

Das Easter Egg besteht darin, dass wenn man wie beim Internet-Trend 6-7 die Tasten W und ArrowUp spamt, ein popup erscheint.

## Arbeitsprozess

Der Raum wurde in Blender modelliert [nach diesem Video](https://youtu.be/NbyGOfWz0yI?si=c2HiwvQ06kRe2g8o) und als `.glb`-Datei exportiert, mit der CLI von Threlte dann in einen typisierten Component umgewandelt. Dieser typisierte Component erlaubt es mir, einzelne Elemente des Modells anzusteuern. Die Kamera-Übergänge nutzen Interpolation anstatt plötzlichen Jumps, damit sich diese smoother anfühlen. Es wurde absichtlich eine orthographische Kamera, anstelle einer perspektivischen Kamera gewählt, um den Low-Poly-Vibe beizubehalten. Für den Kameraübergang wurde GPT-5.5 verwendet, da ich mich noch nicht so ganz mit der Logik auskannte. Schlussendlich habe ich den Mechanismus mit dem Easter-Egg implementiert.

## Sonstiges

Das Projekt ist live unter https://cg.nevthe.dev. Wenn Node.js (https://nodejs.org/en) und Bun (https://bun.sh) installiert sind, kann man das Projekt folgendermassen lokal betrachten:

1. Die Dependencies installieren mit `bun install`
2. Den Dev-Server starten mit `bun run dev`
