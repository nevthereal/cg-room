<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity, OrbitControls } from '@threlte/extras';

	import Mac from './Mac.svelte';

	interactivity();
</script>

<T.DirectionalLight castShadow position={[0, 20, 20]} />

<T.PerspectiveCamera
	makeDefault
	position={[10, 10, 10]}
	oncreate={(ref) => {
		ref.lookAt(0, 1, 0);
	}}
	zoom={1}
/>

<OrbitControls />

<Mac castShadow position={[0, 0.2, 0]} />

<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
	<T.CircleGeometry args={[4, 40]} />
	<T.MeshStandardMaterial color="white" />
</T.Mesh>
