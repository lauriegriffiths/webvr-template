<script lang="ts">
	import { useXR } from '@threlte/xr';
	import JointCollider from './JointBody.svelte';
	const { isHandTracking } = useXR();
</script>

{#if $isHandTracking}
	{#each { length: 25 } as _, jointIndex}
		<JointCollider {jointIndex} hand="left" />
		<JointCollider {jointIndex} hand="right" />
	{/each}
{/if}
