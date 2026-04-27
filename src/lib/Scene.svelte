<script lang="ts">
	import { T } from '@threlte/core';
	import { interactivity, OrbitControls } from '@threlte/extras';

	import Room from './Room.svelte';

	let { cameraMode }: { cameraMode: 'overview' | 'screen' } = $props();

	const classroomTarget = [0, 1, 0] as const;
	const screenTarget = [-2.98, 0.68, 1.9] as const;

	interactivity();
</script>

<T.DirectionalLight castShadow position={[30, 30, 30]} />

{#if cameraMode === 'overview'}
	<T.OrthographicCamera
		makeDefault
		position={[8, 7, 8]}
		zoom={85}
		near={0.1}
		far={100}
		oncreate={(ref) => {
			ref.lookAt(...classroomTarget);
		}}
	>
		<OrbitControls
			enableDamping
			target={[...classroomTarget]}
			minDistance={0.55}
			maxDistance={18}
		/>
	</T.OrthographicCamera>
{:else}
	<T.PerspectiveCamera
		makeDefault
		position={[-2.98, 0.72, 3.25]}
		fov={36}
		near={0.1}
		far={100}
		oncreate={(ref) => {
			ref.lookAt(...screenTarget);
		}}
	>
		<OrbitControls
			enableDamping
			target={[...screenTarget]}
			minDistance={0.55}
			maxDistance={18}
		/>
	</T.PerspectiveCamera>
{/if}

<Room />

<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
	<T.CircleGeometry args={[4, 40]} />
	<T.MeshStandardMaterial color="white" />
</T.Mesh>
