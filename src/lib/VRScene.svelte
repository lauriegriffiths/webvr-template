<script>
	import { XR, Controller, Hand, useHandJoint, pointerControls } from '@threlte/xr';
	import { currentWritable, T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
	import { Spring } from 'svelte/motion';
	import { AmbientLight } from 'three';

	const joint = useHandJoint('left', 'wrist');
	pointerControls('right');

	interactivity();

	const scale = new Spring(1);

	let rotation = 0;
	useTask((delta) => {
		rotation += delta;
	});
</script>

{@debug joint}

<T.PerspectiveCamera
	makeDefault
	position={[10, 10, 10]}
	oncreate={(ref) => {
		ref.lookAt(0, 1, 0);
	}}
/>

<T.DirectionalLight position={[0, 10, 10]} castShadow />
<T.AmbientLight />

<T.Mesh
	rotation.y={rotation}
	position.y={1}
	scale={scale.current}
	onpointerenter={() => {
		scale.target = 1.2;
	}}
	onpointerleave={() => {
		scale.target = 1;
	}}
	castShadow
>
	<T.BoxGeometry args={[0.2, 0.6, 0.2]} />
	<T.MeshStandardMaterial color="hotpink" />
</T.Mesh>

<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
	<T.CircleGeometry args={[4, 40]} />
	<T.MeshStandardMaterial color="white" />
</T.Mesh>

<XR />
<Controller left />
<Controller right />
<Hand left />
<Hand right />
