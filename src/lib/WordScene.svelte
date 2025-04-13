<script lang="ts">
	import { T, useLoader } from '@threlte/core';
	import { Hand, XR, useXR, Headset, useHeadset } from '@threlte/xr';
	import {
		Text,
		Align,
		Text3DGeometry,
		OrbitControls,
		AudioListener,
		HUD,
		useViewport,
		interactivity
	} from '@threlte/extras';
	import { Attractor, Debug, RigidBody, AutoColliders } from '@threlte/rapier';
	import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js';
	import Letter from '$lib/Letter.svelte';
	import PhysicsHands from './hand/PhysicsHands.svelte';

	import Ground from '$lib/physics/Ground.svelte';
	import { AmbientLight } from 'three';
	import { onMount } from 'svelte';

	const headset = useHeadset();

	let headsetPosition = $derived([headset.position.x, headset.position.y, headset.position.z]);

	const WORD_LIST = [
		' こんにちは',
		' さようなら',
		' ありがとう',
		' すみません',
		' おはよう',
		' こんばんは',
		' おやすみなさい',
		' いただきます',
		' ごちそうさまでした'
	];

	const allLetters = WORD_LIST.join('').split('');

	const limit = 5;
	const startColor = 'hotpink';
	let colors = $state(Array(limit).fill(startColor));
	let targetWord = $state('');
	let letters: string[] = $state([]);
	let found: boolean[] = $state([]);
	let hudLetters = $derived(
		letters
			.map((letter, index) => (found[index] ? letter : '?'))
			.slice(1)
			.join('')
	);
	let font = useLoader(FontLoader).load('noto.json');

	let debug = $state(false);

	const ATTRACTOR_STRENGTH = 0.00001;

	//viewport and hud
	const viewport = useViewport();
	interactivity();

	//game

	let score = $state(0);

	const setTargetWord = () => {
		targetWord = WORD_LIST[Math.floor(Math.random() * WORD_LIST.length)];
	};
	const resetGame = () => {
		setTargetWord();
		//reset letters the target word repeated 3 times, shuffled
		letters = [...targetWord.split(''), ...targetWord.split(''), ...targetWord.split('')].sort(
			() => Math.random() - 0.5
		);
		found = Array(targetWord.length).fill(false);
	};

	const letterChosen = (letterIndex: number) => {
		console.log('targetWord', targetWord);
		for (let i = 0; i < targetWord.length; i++) {
			if (targetWord[i] === letters[letterIndex]) {
				found[i] = true;
				score += 1;
			}
		}
	};

	onMount(() => {
		resetGame();
	});
</script>

{#if debug}
	<Debug />
{/if}

<XR>
	<Hand left onpinchend={resetGame} />
	<Hand right onpinchend={() => (debug = !debug)} />

	<PhysicsHands />

	<Text position={[0, 1.7, -1]} text={hudLetters} />
	<Text position={[0, 2.5, -1]} text={score} fontSize={0.2} color="blue" />
	<Headset>
		<Attractor range={500} strength={ATTRACTOR_STRENGTH} />
	</Headset>
	{#snippet fallback()}
		<Attractor range={500} strength={ATTRACTOR_STRENGTH} position={[0.5, 1, 5.2]} />
	{/snippet}
</XR>

<HUD>
	<T.AmbientLight intensity={0.5} />
	<T.Mesh position={[$viewport.width / 2 - 1, $viewport.height / 2 - 1, 0]}>
		<Text3DGeometry
			curveSegments={12}
			text={hudLetters}
			size={0.5}
			depth={0.03}
			font={'/noto.json'}
		/>
		<T.MeshStandardMaterial color="blue" toneMapped={false} />
	</T.Mesh>
</HUD>

{#if $font}
	{#each letters as letter, index (index)}
		{#if !found[index]}
			<T.Group position={[0.1 * (index + 1), 1, 0]}>
				<RigidBody
					onsensorenter={() => letterChosen(index)}
					linearDamping={0.5}
					angularDamping={0.2}
				>
					<AutoColliders shape={'convexHull'} restitution={0} contactForceEventThreshold={10}>
						<T.Mesh onclick={() => letterChosen(index)}>
							<Text3DGeometry
								curveSegments={4}
								text={letter}
								size={0.2}
								depth={0.03}
								font={$font}
							/>
							<T.MeshStandardMaterial color="#FD3F00" toneMapped={true} roughness={0.1} />
						</T.Mesh>
					</AutoColliders>
				</RigidBody>
			</T.Group>
		{/if}
	{/each}
{/if}

<Ground />
<T.PerspectiveCamera makeDefault position={[0.5, 1, 5.2]} oncreate={(ref) => ref.lookAt(0, 0.5, 0)}>
	<OrbitControls enableZoom={false} />
	<AudioListener />
</T.PerspectiveCamera>

<T.AmbientLight />

<T.SpotLight
	position={[1, 8, 1]}
	angle={0.3}
	penumbra={1}
	intensity={30}
	castShadow
	target.x={0}
	target.y={1.8}
	target.z={0}
/>

<!-- <T.Mesh position={[0, 1.7, 0]}>
	<Text3DGeometry curveSegments={12} text="日本" size={0.1} depth={0.03} font={'/noto.json'} />
	<T.MeshStandardMaterial color="green" toneMapped={false} />
</T.Mesh> -->
