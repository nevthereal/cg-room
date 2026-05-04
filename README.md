# Computer Graphics Project

This repository contains a small interactive Computer Graphics project built with SvelteKit, Threlte and Three.js. The application presents a stylised 3D room with an arcade machine and two camera modes:

- `Whole Room`: shows the complete scene composition
- `Arcade screen`: zooms in to the arcade area and lets the user animate the joysticks with the keyboard

## Purpose of the project

The goal of the project is to demonstrate core computer graphics ideas in a web application:

- loading and displaying a 3D scene from a GLB model
- combining multiple light sources for a readable scene
- switching between predefined camera shots
- animating parts of a 3D model through user input
- structuring a graphics scene with reusable Svelte components

## Technology stack

- `SvelteKit` for the web application structure
- `Threlte` as the Svelte-first Three.js integration
- `Three.js` for 3D rendering primitives such as vectors, cameras and lighting
- `TypeScript` for typed scene logic

## Project structure

- [src/routes/+page.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/routes/+page.svelte): page-level UI and camera mode buttons
- [src/lib/Scene.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Scene.svelte): main 3D scene logic, lights, camera transitions and joystick animation
- [src/lib/Room.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Room.svelte): typed GLB room model imported as a Threlte component
- [src/lib/Camera.svelte](/Users/nevillebrem/Developer/Schule/EF/cg/classroom/src/lib/Camera.svelte): currently unused helper component from an earlier iteration
- `static/room-transformed.glb`: optimised room model used by the application
- `models/room.glb`: source model export used to generate the room component

## Interaction

- Use the buttons in the top-right corner to switch camera modes.
- In `Arcade screen` mode, use `W`, `A`, `S`, `D` and the arrow keys to animate the joysticks.

## Notes on the implementation

- The room geometry is imported from Blender as a `.glb` file and then converted into a typed Svelte component.
- The camera uses smooth interpolation instead of instant jumps so transitions feel more polished.
- An orthographic camera is used to keep the composition clean and to reduce perspective distortion.
- The joystick movement is a local animation effect and is not connected to a full arcade game.

## AI and declaration of "independance"

AI was used inside of this project to implement stuff like easing and creating the camera transition (GPT-5.5 inside of Codex). The 3D Model was done entirely by myself, in inspiration of this video: https://youtu.be/NbyGOfWz0yI?si=c2HiwvQ06kRe2g8o.

## Run the project

The project is hosted under https://cg.nevthe.dev. To run the project locally:

Install dependencies and start the development server:

```sh
bun install
bun run dev
```

Useful scripts:

```sh
bun run check
bun run lint
bun run build
```
