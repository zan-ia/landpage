<script lang="ts">
	import site from '$lib/data/site.json';
	import { base } from '$app/paths';

	let scrolled = $state(false);
	let menuOpen = $state(false);

	function handleScroll() {
		scrolled = window.scrollY > 20;
	}

	function toggleMenu() {
		menuOpen = !menuOpen;
	}

	function closeMenu() {
		menuOpen = false;
	}

	function onKeydown(e: KeyboardEvent) {
		if (e.key === 'Escape') closeMenu();
	}

	// Close menu when clicking outside
	function onClickOutside(e: MouseEvent) {
		const target = e.target as HTMLElement;
		if (!target.closest('.header')) closeMenu();
	}

	// Close menu on scroll
	$effect(() => {
		if (menuOpen) {
			window.addEventListener('scroll', closeMenu, { once: true });
			return () => window.removeEventListener('scroll', closeMenu);
		}
	});

	// Close menu on click outside
	$effect(() => {
		if (menuOpen) {
			document.addEventListener('click', onClickOutside);
			return () => document.removeEventListener('click', onClickOutside);
		}
	});

	// Prevent body scroll when menu is open
	$effect(() => {
		if (menuOpen) {
			document.body.style.overflow = 'hidden';
		} else {
			document.body.style.overflow = '';
		}
		return () => { document.body.style.overflow = ''; };
	});

	$effect(() => {
		window.addEventListener('scroll', handleScroll);
		return () => window.removeEventListener('scroll', handleScroll);
	});

	/** Prefix internal paths with SvelteKit base path. */
	const resolveHref = (href: string) => href.startsWith('/') ? base + href : href;
</script>

<svelte:window on:keydown={onKeydown} />

<header class="header" class:header--scrolled={scrolled}>
	<div class="header__inner">
		<a href={resolveHref('/')} class="header__brand" onclick={closeMenu}>
			<span class="material-symbols-outlined header__icon">memory</span>
			<span class="header__title">ZAN.IA</span>
		</a>
		<nav class="header__nav" class:header__nav--open={menuOpen}>
			{#each site.header.navItems as item}
				<a href={resolveHref(item.ref)} class="header__link" onclick={closeMenu}>{item.label}</a>
			{/each}
		</nav>
		<button
			class="header__menu-toggle"
			onclick={(e) => { e.stopPropagation(); toggleMenu(); }}
			aria-label={menuOpen ? 'Fechar menu' : 'Abrir menu'}
			aria-expanded={menuOpen}
		>
			<span class="material-symbols-outlined header__menu-icon">
				{menuOpen ? 'close' : 'menu'}
			</span>
		</button>
	</div>
</header>

{#if menuOpen}
	<!-- svelte-ignore a11y_no_static_element_interactions a11y_click_events_have_key_events -->
	<div class="header__overlay" onclick={closeMenu} onkeydown={(e) => { if (e.key === 'Escape') closeMenu(); }} tabindex="-1"></div>
{/if}

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
		z-index: 52;
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

	/* ── Hamburger Toggle ── */
	.header__menu-toggle {
		display: none;
		background: none;
		border: none;
		cursor: pointer;
		padding: 8px;
		border-radius: var(--radius-sm);
		color: var(--color-on-surface);
		z-index: 52;
		transition: background 0.2s;
	}

	.header__menu-toggle:hover {
		background: rgba(0, 224, 255, 0.08);
	}

	.header__menu-icon {
		font-size: 24px;
	}

	@media (max-width: 767px) {
		.header__menu-toggle {
			display: flex;
			align-items: center;
			justify-content: center;
		}
	}

	/* ── Navigation ── */
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

	/* ── Mobile ── */
	@media (max-width: 767px) {
		.header__nav {
			position: fixed;
			top: 0;
			right: 0;
			max-width: 100%;
			min-width: fit-content;
			height: 100dvh;
			flex-direction: column;
			align-items: stretch;
			justify-content: flex-start;
			gap: 4px;
			padding: 96px 24px 32px;
			background: rgba(12, 19, 36, 0.98);
			backdrop-filter: blur(24px);
			-webkit-backdrop-filter: blur(24px);
			border-left: 1px solid rgba(59, 73, 76, 0.15);
			transform: translateX(100%);
			transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
			z-index: 51;
			overflow-y: auto;
		}

		.header__nav--open {
			transform: translateX(0);
		}

		.header__link {
			font-size: var(--font-size-body-md);
			padding: 14px 16px;
			border-radius: var(--radius-md);
			width: 100%;
			white-space: nowrap;
		}

		.header__link:hover {
			background: rgba(0, 224, 255, 0.1);
		}
	}

	/* ── Overlay ── */
	.header__overlay {
		display: none;
	}

	@media (max-width: 767px) {
		.header__overlay {
			display: block;
			position: fixed;
			inset: 0;
			background: rgba(0, 0, 0, 0.5);
			z-index: 49;
			cursor: default;
		}
	}
</style>
