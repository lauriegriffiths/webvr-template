<script lang="ts">
	import { T, useLoader } from '@threlte/core';
	import { useSuspense, Text3DGeometry, InstancedMesh, Instance, Suspense } from '@threlte/extras';
	import { AutoColliders, Collider, RigidBody } from '@threlte/rapier';
	import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js';

	const size = 0.02;
	const limit = 5;
	const startColor = 'hotpink';
	let colors = $state(Array(limit).fill(startColor));
	const letters = [
		'',
		'あ',
		'い',
		'う',
		'え',
		'お',
		'か',
		'き',
		'く',
		'け',
		'こ',
		'さ',
		'し',
		'す',
		'せ',
		'そ'
	];
	let font = useLoader(FontLoader).load('noto.json');
</script>

{#if $font}
	{#each letters as letter, index (index + 1)}
		<T.Group position={[0.1 * (index + 1), 1, 0]}>
			<RigidBody
				type={'dynamic'}
				onsensorenter={() => (colors[index] = 'blue')}
				linearDamping={3}
				angularDamping={3}
			>
				<AutoColliders shape={'convexHull'} restitution={0} contactForceEventThreshold={10}>
					<T.Mesh>
						<Text3DGeometry curveSegments={4} text={letter} size={0.2} depth={0.03} font={$font} />
						<T.MeshStandardMaterial color="#FD3F00" toneMapped={true} roughness={0.1} />
					</T.Mesh>
				</AutoColliders>
			</RigidBody>
		</T.Group>
	{/each}
{/if}
