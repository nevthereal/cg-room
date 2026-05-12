<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
	import { PressedKeys } from 'runed';
	import { SvelteSet } from 'svelte/reactivity';
	import { MathUtils, Vector3 } from 'three';
	import type { Group, OrthographicCamera } from 'three';

	import Room from './Room.svelte';

	let { cameraMode }: { cameraMode: 'overview' | 'screen' } = $props();

	// Each camera preset defines a position, a point to look at and a zoom level.
	// The scene interpolates between these presets so camera changes feel smooth.
	type CameraShot = {
		position: Vector3;
		target: Vector3;
		zoom: number;
	};
	type StickInput = {
		x: number;
		z: number;
	};

	const shots = {
		overview: {
			position: new Vector3(8, 6.5, 8),
			target: new Vector3(0, 1, 0),
			zoom: 36
		},
		screen: {
			position: new Vector3(0.35, 2.75, 4.6),
			target: new Vector3(-5.1, 2.82, 0.05),
			zoom: 650
		}
	} satisfies Record<'overview' | 'screen', CameraShot>;

	let camera = $state.raw<OrthographicCamera>();
	let leftJoystick = $state.raw<Group>();
	let rightJoystick = $state.raw<Group>();
	const keys = new PressedKeys();
	let fallbackKeys = new SvelteSet<string>();

	const lookAtTarget = new Vector3().copy(shots.overview.target);
	const activeShot = $derived(shots[cameraMode]);

	const handledKeys = new Set([
		'w',
		'a',
		's',
		'd',
		'arrowup',
		'arrowleft',
		'arrowdown',
		'arrowright'
	]);

	const clampAxis = (negativeKey: string, positiveKey: string) => {
		return (
			(keys.has(positiveKey) || fallbackKeys.has(positiveKey) ? 1 : 0) -
			(keys.has(negativeKey) || fallbackKeys.has(negativeKey) ? 1 : 0)
		);
	};

	// In screen mode, WASD and the arrow keys animate the two joysticks
	// to make the arcade machine feel active even though no full game runs on it.
	const leftStick = $derived(
		cameraMode === 'screen'
			? {
					x: clampAxis('a', 'd'),
					z: clampAxis('w', 's')
				}
			: { x: 0, z: 0 }
	);
	const rightStick = $derived(
		cameraMode === 'screen'
			? {
					x: clampAxis('arrowleft', 'arrowright'),
					z: clampAxis('arrowup', 'arrowdown')
				}
			: { x: 0, z: 0 }
	);
	const leftScreenStick = $derived({
		x: leftStick.z,
		z: -leftStick.x
	});
	const rightScreenStick = $derived({
		x: rightStick.z,
		z: -rightStick.x
	});
	const handleKeydown = (event: KeyboardEvent) => {
		const key = event.key.toLowerCase();
		if (!handledKeys.has(key)) return;

		event.preventDefault();
		fallbackKeys.add(key);
	};

	const handleKeyup = (event: KeyboardEvent) => {
		const key = event.key.toLowerCase();
		if (!handledKeys.has(key)) return;

		event.preventDefault();
		fallbackKeys.delete(key);
	};

	interactivity();

	useTask((delta) => {
		if (!camera) return;

		// Exponential easing keeps movement frame-rate independent.
		const ease = 1 - Math.exp(-delta * 3.8);

		camera.position.lerp(activeShot.position, ease);
		lookAtTarget.lerp(activeShot.target, ease);
		camera.zoom = MathUtils.lerp(camera.zoom, activeShot.zoom, ease);
		camera.lookAt(lookAtTarget);
		camera.updateProjectionMatrix();

		const stickEase = 1 - Math.exp(-delta * 12);
		const animateJoystick = (joystick: Group | undefined, input: StickInput) => {
			if (!joystick) return;

			joystick.rotation.x = MathUtils.lerp(joystick.rotation.x, input.z * 0.5, stickEase);
			joystick.rotation.y = MathUtils.lerp(joystick.rotation.y, 0, stickEase);
			joystick.rotation.z = MathUtils.lerp(joystick.rotation.z, -0.26 - input.x * 0.5, stickEase);
		};

		animateJoystick(leftJoystick, leftScreenStick);
		animateJoystick(rightJoystick, rightScreenStick);
	});
</script>

<svelte:window onkeydown={handleKeydown} onkeyup={handleKeyup} />

<!-- The lighting combines ambient, hemisphere and direct light
     so the room stays readable from both camera presets. -->
<T.AmbientLight intensity={1.1} />
<T.HemisphereLight intensity={1.7} color="#f8f1df" groundColor="#5b6472" />
<T.DirectionalLight castShadow intensity={3.2} position={[10, 12, 8]} />
<T.PointLight intensity={7} distance={9} position={[-2.8, 3.2, 3.4]} />

<!-- Orthographic projection avoids perspective distortion and supports
     the clean, presentation-like view used in this graphics study. -->
<T.OrthographicCamera
	makeDefault
	bind:ref={camera}
	position={shots.overview.position.toArray()}
	zoom={shots.overview.zoom}
	near={0.1}
	far={100}
	oncreate={(ref) => {
		ref.lookAt(shots.overview.target);
	}}
/>

<Room bind:joystickLeftRef={leftJoystick} bind:joystickRightRef={rightJoystick} />
