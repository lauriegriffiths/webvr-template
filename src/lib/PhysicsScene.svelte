<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls, AudioListener } from '@threlte/extras';
	import { Debug } from '@threlte/rapier';
	import Emitter from '$lib/physics/Emitter.svelte';
	import Ground from '$lib/physics/Ground.svelte';
	import { XR, Controller, Hand } from '@threlte/xr';
	import { interactivity } from '@threlte/extras';
	interactivity();
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]}>
	<OrbitControls enableZoom={false} />
	<AudioListener />
</T.PerspectiveCamera>

<T.DirectionalLight castShadow position={[8, 20, -3]} />

<T.GridHelper args={[50]} />

<Ground />

<!-- <Debug /> -->

<Emitter />

<XR />
<Controller left />
<Controller right />
<Hand left />
<Hand right />
