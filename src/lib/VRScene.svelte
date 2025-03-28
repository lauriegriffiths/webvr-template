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

	let rotating = $state(true);

	let rotation = $state(0);
	useTask((delta) => {
		if (rotating) {
			rotation += delta;
		}
	});

	const colors = ['red', 'blue', 'green'];

	let color = $state('red');

	const changeColor = () => {
		const currentIndex = colors.indexOf(color);
		const nextIndex = (currentIndex + 1) % colors.length;
		color = colors[nextIndex];
	};

	const onpinchstart = () => {
		scale.target = 2;
	};
	const onpinchend = () => {
		scale.target = 1;
	};

	const onleftpinchstart = () => {
		rotating = false;
	};
	const onleftpinchend = () => {
		rotating = true;
	};
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
	onclick={changeColor}
	castShadow
>
	<T.BoxGeometry args={[0.2, 0.6, 0.2]} />
	<T.MeshStandardMaterial {color} />
</T.Mesh>

<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
	<T.CircleGeometry args={[4, 40]} />
	<T.MeshStandardMaterial color="white" />
</T.Mesh>

<XR />
<Controller left />
<Controller right />
<Hand left onpinchstart={onleftpinchstart} onpinchend={onleftpinchend} />
<Hand right {onpinchstart} {onpinchend} />
