<script lang="ts">
	import { Canvas } from '@threlte/core';
	import Scene from '$lib/Scene.svelte';
	import { onDestroy } from 'svelte';

	let cameraMode = $state<'overview' | 'screen'>('overview');
	let requestedCameraMode = $state<'overview' | 'screen'>('overview');
	let isSwitchingCamera = $state(false);
	let cameraSwitchTimeout: ReturnType<typeof setTimeout> | undefined;
	let cameraFadeTimeout: ReturnType<typeof setTimeout> | undefined;

	const switchCamera = (mode: 'overview' | 'screen') => {
		if (mode === requestedCameraMode) return;

		requestedCameraMode = mode;
		isSwitchingCamera = true;
		clearTimeout(cameraSwitchTimeout);
		clearTimeout(cameraFadeTimeout);

		cameraSwitchTimeout = setTimeout(() => {
			cameraMode = mode;
		}, 150);

		cameraFadeTimeout = setTimeout(() => {
			isSwitchingCamera = false;
		}, 420);
	};

	onDestroy(() => {
		clearTimeout(cameraSwitchTimeout);
		clearTimeout(cameraFadeTimeout);
	});
</script>

<div class="scene-shell">
	<Canvas>
		<Scene {cameraMode} />
	</Canvas>
	<div class="camera-fade" class:visible={isSwitchingCamera}></div>
</div>

<div class="camera-controls" aria-label="Camera controls">
	<button
		class:active={cameraMode === 'overview'}
		type="button"
		disabled={isSwitchingCamera}
		onclick={() => switchCamera('overview')}
	>
		Classroom
	</button>
	<button
		class:active={cameraMode === 'screen'}
		type="button"
		disabled={isSwitchingCamera}
		onclick={() => switchCamera('screen')}
	>
		Arcade screen
	</button>
</div>

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

	.camera-fade {
		position: fixed;
		inset: 0;
		z-index: 5;
		pointer-events: none;
		background: #101010;
		opacity: 0;
		transition: opacity 180ms ease;
	}

	.camera-fade.visible {
		opacity: 0.72;
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
		font: 600 0.9rem/1 system-ui, sans-serif;
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

	button:disabled {
		cursor: default;
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
