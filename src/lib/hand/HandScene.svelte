<script lang="ts">
	import { T } from '@threlte/core';
	import { Hand, XR, useXR } from '@threlte/xr';
	import { Text, Align, Text3DGeometry } from '@threlte/extras';
	import { Attractor, Debug } from '@threlte/rapier';
	import Cubes from './Cube.svelte';
	import PhysicsHands from './PhysicsHands.svelte';
	import Ground from '$lib/physics/Ground.svelte';

	let debug = false;
</script>

{#if debug}
	<Debug />
{/if}

<XR>
	<Hand left onpinchend={() => (debug = !debug)} />
	<Hand right onpinchend={() => (debug = !debug)} />

	<PhysicsHands />

	<Text position={[0, 1.7, -1]} text="Pinch to toggle physics debug." />
</XR>

<Cubes />
<Ground />
<T.PerspectiveCamera
	makeDefault
	position={[0.5, 1, 5.2]}
	oncreate={(ref) => ref.lookAt(0, 0.5, 0)}
/>

<T.AmbientLight />

<T.SpotLight
	position={[1, 8, 1]}
	angle={0.3}
	penumbra={1}
	intensity={30}
	castShadow
	target.x={0}
	target.y={1.8}
	target.z={0}
/>
<!-- <Debug /> -->

<!-- <T.Mesh position={[0, 1.7, 0]}>
	<Text3DGeometry curveSegments={12} text="日本" size={0.1} depth={0.03} font={'/noto.json'} />
	<T.MeshStandardMaterial color="green" toneMapped={false} />
</T.Mesh> -->

<!-- <Attractor range={50} strength={0.00001} position={[0, 1.7, 0]} /> -->
