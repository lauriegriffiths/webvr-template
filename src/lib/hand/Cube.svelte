<script lang="ts">
	import { T } from '@threlte/core';
	import { Text3DGeometry, InstancedMesh, Instance } from '@threlte/extras';
	import { AutoColliders, Collider, RigidBody } from '@threlte/rapier';

	const size = 0.02;
	const limit = 5;
	const startColor = 'hotpink';
	let colors = $state(Array(limit).fill(startColor));
	const letters = ['日', '本', '語', 'は', '難', 'し'];
</script>

<T.Group position={[0, 0.8, 0]}>
	{#each letters as letter, index (index)}
		<RigidBody onsensorenter={() => (colors[index] = 'blue')}>
			<Collider shape="cuboid" args={[size / 2, size / 2, size / 2]} />
			<AutoColliders shape={'convexHull'}>
				<T.Mesh>
					<Text3DGeometry
						curveSegments={1}
						text={letter}
						size={0.1}
						depth={0.03}
						font={'noto.json'}
					/>
					<T.MeshStandardMaterial color={'blue'} toneMapped={false} />
				</T.Mesh>
			</AutoColliders>
			<!-- <T.Mesh>
				<T.BoxGeometry args={[size, size, size]} />
				<T.MeshStandardMaterial roughness={0} metalness={0.2} color={colors[index]} />
			</T.Mesh> -->
		</RigidBody>
	{/each}
</T.Group>
