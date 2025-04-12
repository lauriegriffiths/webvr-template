<script lang="ts" module>
	// const geometry = new BoxGeometry(1, 1, 1);
	const geometry = new SphereGeometry(0.5, 32, 32);
	export const muted = writable(true);
</script>

<script lang="ts">
	import { T } from '@threlte/core';
	import { Collider, RigidBody, type ContactEvent } from '@threlte/rapier';
	import { writable } from 'svelte/store';
	import { BoxGeometry, MeshStandardMaterial, SphereGeometry } from 'three';

	// export let position: Vector3 | undefined = undefined;
	// export let rotation: Euler | undefined = undefined;

	const { position, rotation } = $props();

	let color = $state('red');
	let body: typeof RigidBody = $state();

	const audios: {
		threshold: number;
		volume: number;
		stop: (() => any) | undefined;
		play: ((...args: any[]) => any) | undefined;
		source: string;
	}[] = new Array(9).fill(0).map((_, i) => {
		return {
			threshold: i / 10,
			play: undefined,
			stop: undefined,
			volume: (i + 2) / 10,
			source: `/audio/ball_bounce_${i + 1}.mp3`
		};
	});

	const changeColorBounce = (e) => {
		const currentTranslation = body.translation();
		console.log(body);
		const { x, y, z } = currentTranslation;

		// body.setTranslation({ x, y: y + 1, z });
		body.setLinvel({ x: 0, y: 10, z: 0 });
		color = 'blue';
		e.stopPropagation();
	};
	const bounce = (e) => {
		e.stopPropagation();
		console.log(e);
	};

	let rotationCasted = $derived(rotation ? rotation?.toArray() : [0, 0, 0]);
</script>

<T.Group
	position.x={position?.x}
	position.y={position?.y}
	position.z={position?.z}
	rotation={rotationCasted}
>
	<RigidBody type={'dynamic'} oncontact={() => {}} bind:rigidBody={body}>
		<Collider contactForceEventThreshold={30} restitution={0.4} shape={'ball'} args={[0.5]} />
		<T.Mesh
			castShadow
			receiveShadow
			{geometry}
			onclick={(e) => {
				changeColorBounce(e);
			}}
		>
			<T.MeshStandardMaterial {color} />
		</T.Mesh>
	</RigidBody>
</T.Group>
