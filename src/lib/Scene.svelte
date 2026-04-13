<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';

	import Mac from './Mac.svelte';

	interactivity();

	let rotation = $state(0);
	useTask((delta) => {
		rotation += delta * 0.5;
	});
</script>

<T.DirectionalLight position={[0, 10, 10]} />

<T.PerspectiveCamera
	makeDefault
	position={[10, 10, 10]}
	oncreate={(ref) => {
		ref.lookAt(0, 1, 0);
	}}
	zoom={5}
/>

<Mac rotation.y={rotation} />
