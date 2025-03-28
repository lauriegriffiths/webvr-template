<script>
	import { Canvas } from '@threlte/core';
	import VRScene from '$lib/VRScene.svelte';
	import { VRButton } from '@threlte/xr';
</script>

<Canvas>
	<VRScene />
</Canvas>
<VRButton />
