<script lang="ts">
	import { T } from '@threlte/core';
	import { InstancedMesh, Instance } from '@threlte/extras';
	import { Collider, RigidBody } from '@threlte/rapier';

	const size = 0.02;
	const limit = 100;
	const startColor = 'hotpink';
	let colors = $state(Array(limit).fill(startColor));
</script>

<T.Group position={[0, 1.7, 0]}>
	<InstancedMesh {limit}>
		{#each { length: limit } as _, index (index)}
			{#if colors[index] === 'hotpink'}
				<RigidBody onsensorenter={() => (colors[index] = 'blue')}>
					<Collider shape="cuboid" args={[size / 2, size / 2, size / 2]} />
					<Instance color="hotpink" />
					<T.BoxGeometry args={[size, size, size]} />
					<T.MeshStandardMaterial roughness={0} metalness={0.2} color={colors[index]} />
				</RigidBody>
			{:else}
				<RigidBody>
					<Collider shape="cuboid" args={[size / 2, size / 2, size / 2]} />
					<Instance color="blue" />
					<T.BoxGeometry args={[size, size, size]} />
					<T.MeshStandardMaterial roughness={0} metalness={0.2} color={colors[index]} />
				</RigidBody>
			{/if}
		{/each}
	</InstancedMesh>
</T.Group>
