<!-- The Layout file serves as a broad container for the full site. -->

<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<!-- The `children()` is referring to anything that is contained in a +page.svelte file or any children routes -->
{@render children()}
