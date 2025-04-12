<script lang="ts">
	import {
		XR,
		Controller,
		Hand,
		useHandJoint,
		pointerControls,
		useController,
		useHand
	} from '@threlte/xr';
	import { currentWritable, T, useTask } from '@threlte/core';
	import { interactivity } from '@threlte/extras';
	import { Spring } from 'svelte/motion';
	import { RigidBody, Collider, Debug } from '@threlte/rapier';
	import { BoxGeometry } from 'three';
	import { Text } from '@threlte/extras';
	const joint = useHandJoint('right', 'thumb-tip');
	const leftHand = useHand('left');

	const rightController = useController('right');

	pointerControls('right');

	interactivity();

	let body: typeof RigidBody | undefined = $state();
	let otherBody: typeof RigidBody | undefined = $state();

	const { start, stop } = useTask(
		() => {
			if (joint.current === undefined || body === undefined) return;
			const { x, y, z } = joint.current.position;
			body.setNextKinematicTranslation({ x, y, z });
		},
		{ autoStart: false }
	);
	let radius = $derived(joint?.current?.jointRadius);

	let count = $state(0);
	let change = $state(1);

	useTask((delta) => {
		count += change * delta;
		if (count > 2) {
			change *= -1;
			// count = 0;
		} else if (count < 0) {
			change *= -1;
		}

		if (otherBody) {
			const [x, y, z] = [1, 2, 3];
			otherBody.setNextKinematicTranslation({ x: count, y, z });
		}
	});

	$effect(() => {
		if (body && radius && $joint) {
			start();
		} else {
			stop();
		}
	});

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

	useTask((delta) => {
		if (dragging) {
			if (joint.current) {
				const { x, y, z } = joint.current.position;
				// const { x: x2, y: y2, z: z2 } = joint.current.rotation;
				console.log('joint', x, y, z);
			}
		}
	});
	$effect(() => {
		console.log(otherBody);
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

{#if radius}
	<T.Group position={[0, 1, 0]}>
		<RigidBody bind:rigidBody={body} type="kinematicPosition">
			<Collider shape="ball" args={[0.5]} />
			<T.Mesh>
				<T.SphereGeometry args={[0.5]} />
				<T.MeshStandardMaterial color={'green'} />
			</T.Mesh>
		</RigidBody>
	</T.Group>
{/if}

<T.Group position={[0, 1, 0]}>
	<RigidBody bind:rigidBody={otherBody} type="kinematicPosition">
		<Collider shape="ball" args={[0.5]} />
		<T.Mesh>
			<T.SphereGeometry args={[0.5]} />
			<T.MeshStandardMaterial color={'blue'} />
		</T.Mesh>
	</RigidBody>
</T.Group>
<!-- {#if joint.current}
	<T.Group position={handColliderPosition}>
		<Collider sensor shape={'cuboid'} args={[0.1, 0.1, 0.1]} />
		<T.BoxGeometry args={[0.1, 0.1, 0.1]} />
		<T.MeshStandardMaterial color={'green'} />
	</T.Group>
{/if} -->

<Text
	text={JSON.stringify(otherBody)}
	color="purple"
	fontSize={0.5}
	anchorX="50%"
	anchorY="100%"
	oncreate={(ref) => {
		ref.lookAt(10, 10, 10);
	}}
/>

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

<Debug />
