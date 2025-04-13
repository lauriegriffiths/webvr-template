<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import { VRButton } from '@threlte/xr';
	import { Studio } from '@threlte/studio';
	import WordScene from '$lib/WordScene.svelte';
</script>

<div class="canvas">
	<Canvas>
		<World gravity={[0, 0, 0]}>
			{#if import.meta.env.VITE_ENABLE_STUDIO}
				<Studio>
					<WordScene />
				</Studio>
			{:else}
				<WordScene />
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
