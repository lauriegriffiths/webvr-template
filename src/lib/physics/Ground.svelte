<script lang="ts">
	import { T } from '@threlte/core';
	import { AutoColliders, Collider, RigidBody } from '@threlte/rapier';
</script>

<T.Group position={[0, -0.5, 0]} onclick={() => console.log('ground clicked')}>
	<RigidBody type={'fixed'}>
		<AutoColliders shape={'cuboid'} restitution={0.7}>
			<T.Mesh receiveShadow>
				<T.BoxGeometry args={[10, 1, 10]} />
				<T.MeshStandardMaterial color={'grey'} />
			</T.Mesh>
		</AutoColliders>
	</RigidBody>
</T.Group>
