<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import { VRButton } from '@threlte/xr';
	import HandScene from '$lib/hand/HandScene.svelte';
	import { Studio } from '@threlte/studio';
</script>

<div>
	<Canvas>
		<World gravity={[0, -0.3, 0]}>
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
	div {
		height: 100%;
	}
</style>
