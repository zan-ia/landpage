<script lang="ts">
	let scrolled = $state(false);

	function handleScroll() {
		scrolled = window.scrollY > 20;
	}

	$effect(() => {
		window.addEventListener('scroll', handleScroll);
		return () => window.removeEventListener('scroll', handleScroll);
	});
</script>

<header class="header" class:header--scrolled={scrolled}>
	<div class="header__inner">
		<div class="header__brand">
			<span class="material-symbols-outlined header__icon">memory</span>
			<span class="header__title">ZAN.IA</span>
		</div>
		<button class="header__cta">ACCESS CORE</button>
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

	.header__cta {
		background: var(--color-primary);
		color: var(--color-on-primary);
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		font-weight: 700;
		letter-spacing: 0.1em;
		padding: 8px 16px;
		border-radius: var(--radius-full);
		text-transform: uppercase;
		transition: filter 0.2s, transform 0.15s;
	}

	.header__cta:hover {
		filter: brightness(1.1);
	}

	.header__cta:active {
		transform: scale(0.95);
	}
</style>
