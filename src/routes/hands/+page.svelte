<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import { VRButton } from '@threlte/xr';
	import HandScene from '$lib/hand/HandScene.svelte';
	import { Studio } from '@threlte/studio';
</script>

<div class="canvas">
	<Canvas>
		<World>
			{#if import.meta.env.VITE_ENABLE_STUDIO}
				<Studio>
					<HandScene />
				</Studio>
			{:else}
				<HandScene />
			{/if}
		</World>
	</Canvas>
	<VRButton />
</div>

<style>
	/* global */
	:global(body) {
		margin: 0;
		padding: 0;
	}
	.canvas {
		width: 100vw;
		height: 100vh;
	}
</style>
