<script>
	import { XR, Controller, Hand, useHandJoint, pointerControls } from '@threlte/xr';
	import { currentWritable, T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
	import { Spring } from 'svelte/motion';
	import { Collider } from '@threlte/rapier';
	import { BoxGeometry } from 'three';

	const joint = useHandJoint('right', 'thumb-tip');
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
	let dragging = $state(false);
	let touching = $state(false);

	const onpinchstart = () => {
		if (touching) {
			dragging = true;
		}
	};
	const onpinchend = () => {
		dragging = false;
	};

	const onleftpinchstart = () => {
		rotating = false;
	};
	const onleftpinchend = () => {
		rotating = true;
	};
	const makeBigger = () => {
		scale.target = scale.target * 1.1;
	};

	useTask(() => {
		if (dragging) {
			if (joint.current) {
				const { x, y, z } = joint.current.position;
				// const { x: x2, y: y2, z: z2 } = joint.current.rotation;
				console.log('joint', x, y, z);
			}
		}
	});
</script>

<T.PerspectiveCamera
	makeDefault
	position={[10, 10, 10]}
	oncreate={(ref) => {
		ref.lookAt(0, 1, 0);
	}}
/>

<T.DirectionalLight position={[0, 10, 10]} castShadow />
<T.AmbientLight />

<T.Group position={[0, 1, 0]}>
	<Collider sensor shape={'cuboid'} args={[0.1, 0.1, 0.1]} />
	<T.BoxGeometry args={[0.1, 0.1, 0.1]} />
	<T.MeshStandardMaterial color={'green'} />
</T.Group>

<T.Mesh
	rotation.y={rotation}
	position.y={1}
	scale={scale.current}
	castShadow
	position={[-1, 1, -1]}
>
	<Collider
		onsensorenter={() => {
			touching = true;
			makeBigger();
		}}
		onsensorexit={() => (touching = false)}
		shape={'ball'}
		args={[1]}
	/>
	<T.SphereGeometry args={[0.5, 32, 32]} />
	<T.MeshStandardMaterial {color} />
</T.Mesh>

<T.Mesh rotation.x={-Math.PI / 2} receiveShadow position={[0, 0, 0]}>
	<T.CircleGeometry args={[4, 40]} />
	<T.MeshStandardMaterial color="white" />
</T.Mesh>

<XR />
<Controller left />
<Controller right />
<Hand left onpinchstart={onleftpinchstart} onpinchend={onleftpinchend} />
<Hand right {onpinchstart} {onpinchend} />
