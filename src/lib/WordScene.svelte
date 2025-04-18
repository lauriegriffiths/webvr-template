<script lang="ts">
	import { T, useLoader } from '@threlte/core';
	import { Hand, XR, useXR, Headset, useHeadset } from '@threlte/xr';
	import {
		Text,
		Align,
		Text3DGeometry,
		OrbitControls,
		HUD,
		useViewport,
		interactivity,
		Suspense
	} from '@threlte/extras';
	import { Attractor, Debug, RigidBody, AutoColliders, Collider } from '@threlte/rapier';
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

	const getTargetWord = () => {
		return WORD_LIST[Math.floor(Math.random() * WORD_LIST.length)];
	};

	const allLetters = WORD_LIST.join('').split('');

	let targetWord = $state(getTargetWord());
	let letters: string[] = $derived([...targetWord.split('')]);
	let found: boolean[] = $state(Array(targetWord.length).fill(false));
	let hudLetters = $derived(
		targetWord
			.split('')
			.map((letter, index) => (found[index] ? letter : '?'))
			.splice(1)
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

	$effect(() => {
		if (found.length <= 1) {
			return;
		}
		//count number of trues in found
		const foundCount = found.reduce((acc, val) => (val ? acc + 1 : acc), 0);
		const wordComplete = foundCount >= found.length - 1;
		if (wordComplete) {
			console.log('Word complete!');
			resetGame();
		}
	});
	const resetGame = () => {
		const tw = getTargetWord();
		targetWord = tw;
		found = Array(tw.length).fill(false);
	};

	const letterChosen = (letterIndex: number) => {
		console.log(`Letter chosen: ${letters[letterIndex]}`);

		//find the position of the first false in found (excluding position 0)
		const firstFalseIndex = found.findIndex((val, index) => !val && index > 0);

		//compare the clicked letter with the letter at the first false index

		if (firstFalseIndex !== -1 && letters[letterIndex] === targetWord[firstFalseIndex]) {
			found[firstFalseIndex] = true;
			score += 1;
		} else {
			console.log('Wrong letter!');
		}

		// for (let i = 0; i < targetWord.length; i++) {
		// 	if (targetWord[i] === letters[letterIndex]) {
		// 		found[i] = true;
		// 		score += 1;
		// 	}
		// }
	};

	onMount(() => {});
</script>

{#if debug}
	<Debug />
{/if}

<XR>
	<Hand left />
	<Hand right />

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

{#each letters as letter, index (index)}
	{#if found.length > 0 && !found[index]}
		<T.Group position={[0.1 * (index + 1), 1, 0]}>
			<RigidBody onsensorenter={() => letterChosen(index)} linearDamping={0.5} angularDamping={0.2}>
				{#if $font}
					<Collider shape={'ball'} args={[0.1]} />
					<AutoColliders shape={'convexHull'} restitution={0}>
						<T.Mesh onclick={() => letterChosen(index)}>
							<Text3DGeometry
								curveSegments={1}
								text={letter}
								size={0.2}
								depth={0.03}
								font={$font}
							/>
							<T.MeshStandardMaterial color="#FD3F00" toneMapped={true} roughness={0.1} />
						</T.Mesh>
					</AutoColliders>
				{:else}
					<AutoColliders shape={'ball'} restitution={0} contactForceEventThreshold={10}>
						<T.Mesh onclick={() => letterChosen(index)}>
							<T.SphereGeometry args={[0.1]} />
							<T.MeshStandardMaterial color="#FD3F00" toneMapped={true} roughness={0.1} />
						</T.Mesh>
					</AutoColliders>
				{/if}
			</RigidBody>
		</T.Group>
	{/if}
{/each}

<Ground />
<T.PerspectiveCamera makeDefault position={[0.5, 1, 5.2]} oncreate={(ref) => ref.lookAt(0, 0.5, 0)}>
	<OrbitControls enableZoom={false} />
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
