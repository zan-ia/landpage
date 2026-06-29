<script lang="ts">
	import { base } from '$app/paths';

	let {
		badgeIcon = 'bolt',
		badgeText = '',
		title = '',
		highlight = '',
		subtitle = '',
		primaryCtaLabel = '',
		primaryCtaHref = '',
		primaryCtaIcon = 'chat',
		secondaryCtaLabel = '',
		secondaryCtaHref = '',
		secondaryCtaIcon = 'arrow_forward',
		scrollIndicator = false,
		bgGradient = 'center'
	}: {
		badgeIcon?: string;
		badgeText?: string;
		title?: string;
		highlight?: string;
		subtitle?: string;
		primaryCtaLabel?: string;
		primaryCtaHref?: string;
		primaryCtaIcon?: string;
		secondaryCtaLabel?: string;
		secondaryCtaHref?: string;
		secondaryCtaIcon?: string;
		scrollIndicator?: boolean;
		bgGradient?: 'center' | 'left' | 'right';
	} = $props();

	const gradientMap: Record<string, string> = {
		center: 'radial-gradient(ellipse at 50% 20%, rgba(0, 224, 255, 0.1) 0%, transparent 55%), radial-gradient(ellipse at 80% 80%, rgba(0, 224, 255, 0.04) 0%, transparent 45%)',
		left: 'radial-gradient(ellipse at 30% 40%, rgba(0, 224, 255, 0.08) 0%, transparent 55%), radial-gradient(ellipse at 70% 70%, rgba(186, 242, 255, 0.04) 0%, transparent 50%)',
		right: 'radial-gradient(ellipse at 20% 50%, rgba(0, 224, 255, 0.08) 0%, transparent 60%), radial-gradient(ellipse at 80% 30%, rgba(0, 224, 255, 0.04) 0%, transparent 50%)'
	};

	const bg = $derived(gradientMap[bgGradient] ?? gradientMap.center);

	/** Prefix internal paths (starting with '/') with SvelteKit base path. */
	const resolveHref = (href: string) => href.startsWith('/') ? base + href : href;
</script>

<section class="page-hero">
	<div class="page-hero__bg" style="background: {bg}"></div>
	<div class="page-hero__content">
		{#if badgeText}
			<div class="page-hero__badge">
				<span class="material-symbols-outlined page-hero__badge-icon">{badgeIcon}</span>
				<span class="page-hero__badge-text">{badgeText}</span>
			</div>
		{/if}
		<h1 class="page-hero__title">
			{title}<br class="hide-mobile" />
			{#if highlight}
				<span class="page-hero__highlight">{highlight}</span>
			{/if}
		</h1>
		{#if subtitle}
			<p class="page-hero__subtitle">{subtitle}</p>
		{/if}
		<div class="page-hero__actions">
			{#if primaryCtaLabel && primaryCtaHref}
				<a class="page-hero__cta page-hero__cta--primary" href={primaryCtaHref} target="_blank" rel="noopener noreferrer">
					<span class="material-symbols-outlined">{primaryCtaIcon}</span>
					{primaryCtaLabel}
				</a>
			{/if}
			{#if secondaryCtaLabel && secondaryCtaHref}
				<a class="page-hero__cta page-hero__cta--secondary" href={resolveHref(secondaryCtaHref)}>
				<a class="page-hero__cta page-hero__cta--secondary" href={secondaryCtaHref}>
					<span class="material-symbols-outlined">{secondaryCtaIcon}</span>
					{secondaryCtaLabel}
				</a>
			{/if}
		</div>
	</div>
	{#if scrollIndicator}
		<div class="page-hero__scroll-indicator">
			<span class="material-symbols-outlined">keyboard_double_arrow_down</span>
		</div>
	{/if}
</section>

<style>
	.page-hero {
		position: relative;
		min-height: 90vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		text-align: center;
		padding: 120px var(--spacing-margin-mobile) 80px;
		overflow: hidden;
	}

	.page-hero__bg {
		position: absolute;
		inset: 0;
		z-index: 0;
	}

	.page-hero__content {
		position: relative;
		z-index: 10;
		max-width: 48rem;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 24px;
	}

	.page-hero__badge {
		display: inline-flex;
		align-items: center;
		gap: 8px;
		padding: 4px 16px;
		border-radius: var(--radius-full);
		border: 1px solid rgba(0, 224, 255, 0.3);
		background: rgba(0, 224, 255, 0.1);
		color: var(--color-primary-fixed-dim);
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		letter-spacing: var(--letter-spacing-label-sm);
	}

	.page-hero__badge-icon {
		font-size: 14px;
		font-variation-settings: 'FILL' 1;
	}

	.page-hero__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		font-weight: var(--font-weight-display-lg);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
		letter-spacing: var(--letter-spacing-tight);
	}

	.page-hero__highlight {
		background: linear-gradient(135deg, var(--color-primary-container), var(--color-primary-fixed-dim));
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
	}

	.page-hero__subtitle {
		font-family: var(--font-body);
		font-size: var(--font-size-body-lg);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-lg);
		max-width: 36rem;
	}

	.page-hero__actions {
		display: flex;
		flex-wrap: wrap;
		gap: 16px;
		justify-content: center;
		margin-top: 8px;
	}

	.page-hero__cta {
		display: inline-flex;
		align-items: center;
		gap: 8px;
		padding: 14px 28px;
		border-radius: var(--radius-sm);
		font-family: var(--font-display);
		font-size: var(--font-size-body-md);
		font-weight: var(--font-weight-semibold);
		text-decoration: none;
		transition: transform 0.2s, box-shadow 0.3s;
	}

	.page-hero__cta:hover {
		transform: translateY(-2px);
	}

	.page-hero__cta--primary {
		background: var(--color-primary-container);
		color: var(--color-on-primary-container);
		box-shadow: var(--shadow-glow-primary);
	}

	.page-hero__cta--primary:hover {
		box-shadow: var(--shadow-glow-primary-hover);
	}

	.page-hero__cta--secondary {
		background: transparent;
		color: var(--color-on-surface);
		border: 1px solid var(--color-outline-variant);
	}

	.page-hero__cta--secondary:hover {
		border-color: var(--color-primary-container);
	}

	.page-hero__scroll-indicator {
		position: absolute;
		bottom: 24px;
		z-index: 10;
		color: var(--color-primary-container);
		opacity: 0.5;
		animation: bounce 2s ease-in-out infinite;
	}

	@keyframes bounce {
		0%, 100% { transform: translateY(0); }
		50% { transform: translateY(8px); }
	}

	@media (min-width: 768px) {
		.page-hero__title {
			font-size: var(--font-size-display-lg);
		}
	}
</style>
