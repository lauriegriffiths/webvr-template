<script lang="ts">
	import { useTask } from '@threlte/core';
	import { handJoints, useHandJoint } from '@threlte/xr';
	import type { RigidBody as RapierRigidBody } from '@dimforge/rapier3d-compat';
	import { Collider, RigidBody } from '@threlte/rapier';
	import { T } from '@threlte/core';

	export let jointIndex: number;
	export let hand: 'left' | 'right';

	let body: RapierRigidBody;

	$: handJointName = handJoints[jointIndex];

	const joint = useHandJoint(hand, handJoints[jointIndex]!);

	const { start, stop } = useTask(
		() => {
			if (joint.current === undefined || body === undefined) return;

			const { x, y, z } = joint.current.position;
			body.setNextKinematicTranslation({ x, y, z });
		},
		{ autoStart: false }
	);

	$: radius = $joint?.jointRadius;

	$: if (body && radius && $joint) {
		start();
	} else {
		stop();
	}
</script>

{#if radius}
	<RigidBody bind:rigidBody={body} type="kinematicPosition">
		{#if handJointName === 'index-finger-tip'}
			<Collider sensor shape="ball" args={[radius * 2]} />
			<T.Mesh>
				<T.SphereGeometry args={[radius * 2]} />
				<T.MeshStandardMaterial color="hotpink" />
			</T.Mesh>
		{:else}
			<Collider shape="ball" args={[radius]} />
		{/if}
	</RigidBody>
{/if}
