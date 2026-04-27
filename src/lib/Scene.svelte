<script lang="ts">
	import { T } from '@threlte/core';
	import { interactivity, OrbitControls } from '@threlte/extras';

	import Room from './Room.svelte';

	let { cameraMode }: { cameraMode: 'overview' | 'screen' } = $props();

	const classroomTarget = [0, 1, 0] as const;
	const screenTarget = [-5.05, 2.55, 0.05] as const;

	interactivity();
</script>

<T.AmbientLight intensity={1.1} />
<T.HemisphereLight intensity={1.7} color="#f8f1df" groundColor="#5b6472" />
<T.DirectionalLight castShadow intensity={3.2} position={[10, 12, 8]} />

{#if cameraMode === 'overview'}
	<T.OrthographicCamera
		makeDefault
		position={[8, 6.5, 8]}
		zoom={70}
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
		position={[0.35, 2.75, 4.6]}
		fov={18}
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
