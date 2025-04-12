<script>
	import { Canvas } from '@threlte/core';
	import VRScene from '$lib/VRScene.svelte';
	import { VRButton } from '@threlte/xr';
	import { Studio } from '@threlte/studio';
</script>

<Canvas>
	{#if import.meta.env.VITE_ENABLE_STUDIO}
		<Studio>
			<VRScene />
		</Studio>
	{:else}
		<VRScene />
	{/if}
</Canvas>
<VRButton />
