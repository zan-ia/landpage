<script lang="ts">
	import site from '$lib/data/site.json';
	import { base } from '$app/paths';

	let scrolled = $state(false);

	function handleScroll() {
		scrolled = window.scrollY > 20;
	}

	$effect(() => {
		window.addEventListener('scroll', handleScroll);
		return () => window.removeEventListener('scroll', handleScroll);
	});

	/** Prefix internal paths with SvelteKit base path. */
	const resolveHref = (href: string) => href.startsWith('/') ? base + href : href;
</script>

<header class="header" class:header--scrolled={scrolled}>
	<div class="header__inner">
		<a href={resolveHref('/')} class="header__brand">
			<span class="material-symbols-outlined header__icon">memory</span>
			<span class="header__title">ZAN.IA</span>
		</a>
		<nav class="header__nav">
			{#each site.header.navItems as item}
				<a href={resolveHref(item.ref)} class="header__link">{item.label}</a>
			{/each}
		</nav>
	</div>
</header>

<style>
	.header {
		position: fixed;
		top: 0;
		left: 50%;
		transform: translateX(-50%);
		width: 100%;
		max-width: var(--spacing-container-max);
		z-index: 50;
		padding: 16px var(--spacing-margin-mobile);
		display: flex;
		justify-content: space-between;
		align-items: center;
		background: rgba(12, 19, 36, 0.8);
		backdrop-filter: blur(24px);
		-webkit-backdrop-filter: blur(24px);
		border-bottom: 1px solid rgba(59, 73, 76, 0.15);
		transition: background 0.3s, box-shadow 0.3s;
	}

	.header--scrolled {
		background: rgba(12, 19, 36, 0.95);
		box-shadow: 0 1px 10px rgba(0, 0, 0, 0.3);
	}

	@media (min-width: 768px) {
		.header {
			padding: 16px var(--spacing-margin-desktop);
		}
	}

	.header__inner {
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 100%;
	}

	.header__brand {
		display: flex;
		align-items: center;
		gap: 8px;
		text-decoration: none;
	}

	.header__brand:hover {
		opacity: 0.8;
	}

	.header__icon {
		color: var(--color-primary);
		font-size: 24px;
	}

	.header__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: 700;
		letter-spacing: -0.05em;
		color: var(--color-primary);
	}

	.header__nav {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.header__link {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		font-weight: 500;
		letter-spacing: var(--letter-spacing-label-sm);
		color: var(--color-on-surface);
		opacity: 0.6;
		padding: 8px 12px;
		border-radius: var(--radius-full);
		text-decoration: none;
		transition: opacity 0.2s, background 0.2s;
	}

	.header__link:hover {
		opacity: 1;
		background: rgba(0, 224, 255, 0.08);
	}
</style>
