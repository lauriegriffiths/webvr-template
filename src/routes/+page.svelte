<script>
	import { Canvas } from '@threlte/core';
	import VRScene from '$lib/VRScene.svelte';
	import { VRButton } from '@threlte/xr';
	import { Studio } from '@threlte/studio';
</script>

<Canvas>
	<Studio>
		<VRScene />
	</Studio>
</Canvas>
<VRButton />
