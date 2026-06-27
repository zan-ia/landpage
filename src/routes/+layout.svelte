<script lang="ts">
	import '$lib/app.css';
	import Header from '$lib/components/Header.svelte';
	import Footer from '$lib/components/Footer.svelte';

	let { children } = $props();
</script>

<Header />
<main class="layout__main">
	{@render children()}
</main>
<Footer />

<style>
	/* Estilos de layout — tokens de corpo estão em app.css */
	.layout__main {
		padding-top: var(--spacing-2xl); /* 96px = 2 * 48px */
	}
</style>
