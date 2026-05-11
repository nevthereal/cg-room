# Computer Graphics Project

Dieses Repository enthält ein interaktives Computer Graphics Projekt, das mit SvelteKit und Threlte (Three.js under the hood) kreiert wurde. Das Projekt ist ein 3D-Modell eines Zimmers mit Retro-Geräten, spezifischer einem Arcade-Automaten und einem Tisch mit Fernseher und einer Art Atari. Im Menu des Web UIs kann man zwischen folgenden Perspektiven wechseln:

- `Whole Room`: zeigt die gesamte Komposition
- `Arcade screen`: zoomt zum Bildschirm des Arcade. Von hier aus kann man die Joysticks kontrollieren

## Lernziele

Das Ziel des Projekt war es, ein in Blender selbst modelliertes low-poly Modell im Web Interaktiv zu machen. Dieses low-poly Modell wurde als GLB-File exportiert und mit Kameras und ein paar Lichtern zum Leben erweckt. Die Joysticks des Arcade-Automaten wurden auch programmatisch animiert. Die ganze Szene wäre rein theoretisch sehr "reusable" und "composable", da die Modelle und Teile der Szene in mehrheitlich separaten Komponenten leben.

## Tech-Stack

Für die Vervollständigung wurden folgende Technologien angewendet.

- `Svelte` (https://svelte.dev), eine Templating-Sprache, die es erlaubt, HTML und TypeScript sehr nah aneinander zu schreiben. SvelteKit stellt ein Meta-Framework dar, das für die Web-Applikations Struktur benutzt wurde, da ich mich da bereits gut auskenne und man sehr einfach eine Web-App entwickeln und auch auf Vercel (https://vercel.com) deployen kann.
- `Threlte` (https://threlte.xyz) als die Verbindungsebene zwischen Svelte und Three.js
- `Three.js` (https://threejs.org) viele 3D-primitives für das Web, basierend auf WebGL.
- `TypeScript`, ein Superset für JavaScript mit statischen Typen. Sehr praktisch für diese Art von Development

## Projectstruktur

- [src/routes/+page.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/routes/+page.svelte): page-level UI und Kamera-Modus Buttons.
- [src/lib/Scene.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Scene.svelte): 3D Szene, sowie die Kamera und Joystick-Logik
- [src/lib/Room.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Room.svelte): Auto-generierter Svelte-Component aus dem GLB Modell
- `static/room-transformed.glb`: optimisertes 3D-Modell, generiert von dem CLI
- `models/room.glb`: exportiertes 3D-Modell aus Blender

## Interaktion

Die Buttons oben rechts dienen dazu, zwischen den zwei Kamera-Modi (oder Perspektiven) zu wechseln. Damit das 3D-Modell angemessen gross ist, muss man im browser unter umständen rein- oder rauszoomen. Wenn man sich im `Arcade screen`-Modus befindet, kann man mit `W`, `A`, `S` und `D` den linken Joystick bedienen und mit den Pfeiltasten den rechten.

## Bemerkungen zur Implementierung

Der Raum wurde in Blender modelliert und als `.glb`-Datei exportiert, mit dem CLI von Threlte dann in einen typisierten Component umgewandelt. Die Kamera-Übergänge nutzen Interpolation anstatt plötzlichen Jumps, damit sich diese smoother anfühlen. Es wurde absichtlich eine orthographische Kamera, anstelle einer perspektivischen Kamera gewählt, um den Low-Poly-Vibe beizubehalten. 

## KI und Eigenständigkeitserklärung

KI wurde für die Implementierung der Kamera-Transition und der Animation der Joysticks benutzt. Genauer gesagt GPT-5.5 in der Codex-App von OpenAI. Das 3D-Modell wurde komplett von mir selber modelliert, um Blender zu "wiedererlernen" habe ich mich auf dieses Video bezogen: https://youtu.be/NbyGOfWz0yI?si=c2HiwvQ06kRe2g8o.

## Sonstiges

Das Projekt ist live unter https://cg.nevthe.dev. Wenn Node.js (https://nodejs.org/en) und Bun (https://bun.sh) installiert sind, kann man das Projekt folgendermassen lokal betrachten:

1. Die Dependencies installieren mit `bun install`
2. Den Dev-Server starten mit `bun run dev`
