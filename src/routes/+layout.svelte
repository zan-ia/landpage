<script lang="ts">
	import '$lib/app.css';
	import Header from '$lib/components/Header.svelte';
	import Footer from '$lib/components/Footer.svelte';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href="/favicon.ico" />
</svelte:head>

<Header />
<main class="pt-24">
	{@render children()}
</main>
<Footer />

<style>
	:global(body) {
		background-color: #070d1f;
		color: #dce1fb;
		font-family: 'Geist', sans-serif;
		overflow-x: hidden;
	}

	:global(.pt-24) {
		padding-top: 96px;
	}
</style>
