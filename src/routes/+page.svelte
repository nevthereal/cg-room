<script lang="ts">
	import { Canvas } from '@threlte/core';
	import Scene from '$lib/Scene.svelte';
	import { fade } from 'svelte/transition';

	// The page only manages the UI state for the camera.
	// All 3D rendering happens inside Scene.svelte.
	let cameraMode = $state<'overview' | 'screen'>('overview');
	let showEasterEgg = $state(false);

	const switchCamera = (mode: 'overview' | 'screen') => {
		cameraMode = mode;
	};

	const revealEasterEgg = () => {
		showEasterEgg = true;
	};

	const closeEasterEggFromBackdrop = (event: MouseEvent) => {
		if (event.currentTarget === event.target) {
			showEasterEgg = false;
		}
	};
</script>

<div class="scene-shell">
	<Canvas>
		<Scene {cameraMode} onEasterEgg={revealEasterEgg} />
	</Canvas>
</div>

<div class="camera-controls" aria-label="Camera controls">
	<!-- These buttons let the viewer compare the full room composition
	     with a close study of the arcade machine. -->
	<button
		class:active={cameraMode === 'overview'}
		type="button"
		onclick={() => switchCamera('overview')}
	>
		Whole Room
	</button>
	<button
		class:active={cameraMode === 'screen'}
		type="button"
		onclick={() => switchCamera('screen')}
	>
		Arcade screen
	</button>
</div>

{#if showEasterEgg}
	<div
		class="easter-egg-backdrop"
		role="presentation"
		transition:fade
		onclick={closeEasterEggFromBackdrop}
	>
		<div
			class="easter-egg-popup"
			role="dialog"
			aria-modal="true"
			aria-labelledby="easter-egg-title"
			tabindex="-1"
		>
			<button
				class="close-button"
				type="button"
				aria-label="Close"
				onclick={() => (showEasterEgg = false)}
			>
				&times;
			</button>
			<h2 id="easter-egg-title">Bravo, du hast das 67 Easter Egg gefunden.</h2>
			<a href="https://www.youtube.com/watch?v=dQw4w9WgXcQ" target="_blank" rel="noreferrer">
				Zum geheimen Link
			</a>
		</div>
	</div>
{/if}

<style>
	:global(body) {
		margin: 0;
		background: #151515;
	}

	:global(html),
	:global(body) {
		width: 100%;
		height: 100%;
		overflow: hidden;
	}

	.scene-shell {
		position: relative;
		width: 100vw;
		height: 100vh;
	}

	.camera-controls {
		position: fixed;
		top: 1.25rem;
		right: 1.25rem;
		z-index: 10;
		display: flex;
		gap: 0.35rem;
		padding: 0.35rem;
		border: 1px solid rgb(255 255 255 / 0.18);
		border-radius: 999px;
		background: rgb(16 18 20 / 0.72);
		box-shadow: 0 12px 36px rgb(0 0 0 / 0.22);
		backdrop-filter: blur(14px);
	}

	button {
		min-width: 8.5rem;
		border: 0;
		border-radius: 999px;
		padding: 0.72rem 1rem;
		background: transparent;
		color: rgb(255 255 255 / 0.72);
		font:
			600 0.9rem/1 system-ui,
			sans-serif;
		cursor: pointer;
		transition:
			background 160ms ease,
			color 160ms ease,
			transform 160ms ease;
	}

	button:hover {
		color: white;
	}

	button:focus-visible {
		outline: 2px solid #f8d66d;
		outline-offset: 2px;
	}

	button.active {
		background: #f8d66d;
		color: #1a1711;
		box-shadow: 0 6px 18px rgb(248 214 109 / 0.28);
	}

	.easter-egg-backdrop {
		position: fixed;
		inset: 0;
		z-index: 20;
		display: grid;
		place-items: center;
		padding: 1rem;
		background: rgb(0 0 0 / 0.48);
		backdrop-filter: blur(8px);
	}

	.easter-egg-popup {
		position: relative;
		width: min(28rem, 100%);
		border: 1px solid rgb(255 255 255 / 0.18);
		border-radius: 0.5rem;
		padding: 2rem;
		background: rgb(20 22 24 / 0.94);
		box-shadow: 0 18px 56px rgb(0 0 0 / 0.36);
		color: white;
		text-align: center;
	}

	.easter-egg-popup h2 {
		margin: 0 0 1.2rem;
		font:
			700 1.35rem/1.25 system-ui,
			sans-serif;
	}

	.easter-egg-popup a {
		color: #f8d66d;
		font:
			700 1rem/1 system-ui,
			sans-serif;
		text-decoration-thickness: 0.12em;
		text-underline-offset: 0.22em;
	}

	.close-button {
		position: absolute;
		top: 0.55rem;
		right: 0.55rem;
		display: grid;
		width: 2rem;
		height: 2rem;
		min-width: 0;
		place-items: center;
		padding: 0;
		border-radius: 999px;
		color: rgb(255 255 255 / 0.72);
		font:
			400 1.4rem/1 system-ui,
			sans-serif;
	}

	.close-button:hover {
		background: rgb(255 255 255 / 0.1);
	}

	@media (max-width: 520px) {
		.camera-controls {
			right: 0.75rem;
			left: 0.75rem;
		}

		button {
			min-width: 0;
			flex: 1;
			padding-inline: 0.65rem;
		}
	}
</style>
