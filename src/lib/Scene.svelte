<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
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
	const lookAtTarget = new Vector3().copy(shots.overview.target);
	const activeShot = $derived(shots[cameraMode]);

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
