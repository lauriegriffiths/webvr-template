<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import { VRButton } from '@threlte/xr';
	import HandScene from '$lib/hand/HandScene.svelte';
</script>

<div>
	<Canvas>
		<World gravity={[0, -0.3, 0]}>
			<HandScene />
		</World>
	</Canvas>
	<VRButton />
</div>

<style>
	div {
		height: 100%;
	}
</style>
