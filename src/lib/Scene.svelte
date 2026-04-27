<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
	import { PressedKeys } from 'runed';
	import { SvelteSet } from 'svelte/reactivity';
	import { MathUtils, OrthographicCamera, Vector3 } from 'three';

	import Room from './Room.svelte';

	let { cameraMode }: { cameraMode: 'overview' | 'screen' } = $props();

	type CameraShot = {
		position: Vector3;
		target: Vector3;
		zoom: number;
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
	const keys = new PressedKeys();
	let fallbackKeys = new SvelteSet<string>();

	const lookAtTarget = new Vector3().copy(shots.overview.target);
	const activeShot = $derived(shots[cameraMode]);
	const showArcadeControls = $derived(cameraMode === 'screen');

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

		const ease = 1 - Math.exp(-delta * 3.8);

		camera.position.lerp(activeShot.position, ease);
		lookAtTarget.lerp(activeShot.target, ease);
		camera.zoom = MathUtils.lerp(camera.zoom, activeShot.zoom, ease);
		camera.lookAt(lookAtTarget);
		camera.updateProjectionMatrix();
	});
</script>

<svelte:window onkeydown={handleKeydown} onkeyup={handleKeyup} />

<T.AmbientLight intensity={1.1} />
<T.HemisphereLight intensity={1.7} color="#f8f1df" groundColor="#5b6472" />
<T.DirectionalLight castShadow intensity={3.2} position={[10, 12, 8]} />
<T.PointLight intensity={7} distance={9} position={[-2.8, 3.2, 3.4]} />

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

<Room />

{#snippet ArcadeStick(x: number, z: number)}
	<T.Group rotation.x={z * 0.62} rotation.z={-x * 0.62}>
		<T.Mesh position.y={0.18} rotation.x={Math.PI / 2}>
			<T.CylinderGeometry args={[0.03, 0.03, 0.38, 12]} />
			<T.MeshStandardMaterial color="#b7b4ac" roughness={0.45} metalness={0.35} />
		</T.Mesh>
		<T.Mesh position.y={0.4}>
			<T.SphereGeometry args={[0.11, 18, 18]} />
			<T.MeshStandardMaterial color="#ff5b62" roughness={0.25} />
		</T.Mesh>
	</T.Group>
{/snippet}

<T.Group visible={showArcadeControls}>
	<T.Group position={[-5.42, 1.62, 0.53]} rotation.y={-0.18}>
		{@render ArcadeStick(leftStick.x, leftStick.z)}
	</T.Group>
	<T.Group position={[-4.74, 1.62, 0.53]} rotation.y={-0.18}>
		{@render ArcadeStick(rightStick.x, rightStick.z)}
	</T.Group>
</T.Group>
