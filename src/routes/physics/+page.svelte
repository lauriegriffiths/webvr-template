<script>
	import { Canvas } from '@threlte/core';
	import PhysicsScene from '$lib/PhysicsScene.svelte';
	import { VRButton } from '@threlte/xr';
	import { Studio } from '@threlte/studio';
	import { World } from '@threlte/rapier';
</script>

<div class="canvas">
	<Canvas>
		<World>
			{#if import.meta.env.VITE_ENABLE_STUDIO}
				<Studio>
					<PhysicsScene />
				</Studio>
			{:else}
				<PhysicsScene />
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
