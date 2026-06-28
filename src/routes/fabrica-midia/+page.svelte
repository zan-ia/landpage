<script lang="ts">
	import site from '$lib/data/site.json';
	import fabrica from '$lib/data/fabrica-midia.json';
	import PageHero from '$lib/components/PageHero.svelte';
	import FeatureCard from '$lib/components/FeatureCard.svelte';
	import ProcessStep from '$lib/components/ProcessStep.svelte';
	import PageCTA from '$lib/components/PageCTA.svelte';

	const whatsappUrl = `https://wa.me/${site.whatsapp.phone}?text=${encodeURIComponent(site.whatsapp.message)}`;
</script>

<svelte:head>
	<title>Fábrica de Mídia IA | ZAN.IA</title>
	<meta name="description" content="Fábrica de Mídia IA: produção cinematográfica Supervisionada. E-books, Audiobooks, Podcasts e Vídeos Dark gerados por IA com supervisão de especialistas veteranos." />
</svelte:head>

<!-- ═══════════════════════════════════════════ -->
<!-- HERO                                        -->
<!-- ═══════════════════════════════════════════ -->
<PageHero
	badgeIcon={fabrica.hero.badgeIcon}
	badgeText={fabrica.hero.badgeText}
	title={fabrica.hero.title}
	highlight={fabrica.hero.highlight}
	subtitle={fabrica.hero.subtitle}
	primaryCtaLabel={fabrica.hero.primaryCta.label}
	primaryCtaHref={whatsappUrl}
	primaryCtaIcon={fabrica.hero.primaryCta.icon}
	secondaryCtaLabel={fabrica.hero.secondaryCta.label}
	secondaryCtaHref={fabrica.hero.secondaryCta.href}
	secondaryCtaIcon={fabrica.hero.secondaryCta.icon}
	scrollIndicator={true}
	bgGradient="left"
/>

<!-- ═══════════════════════════════════════════ -->
<!-- SERVIÇOS ESPECIALIZADOS                     -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section" id="servicos">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{fabrica.services.sectionTitle}</h2>
		</div>
		<div class="page-section__grid page-section__grid--2col">
			{#each fabrica.services.cards as card}
				<FeatureCard
					icon={card.icon}
					title={card.title}
					description={card.description}
					tags={card.tags ?? []}
					featured={card.featured ?? false}
				/>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- FUSÃO PERFEITA                              -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="fusion-card glass-panel">
			<h2 class="fusion-card__title">{fabrica.fusion.title}</h2>
			<p class="fusion-card__text">{fabrica.fusion.text}</p>
			<ul class="fusion-card__list">
				{#each fabrica.fusion.items as item}
					<li class="fusion-card__item">
						<span class="material-symbols-outlined fusion-card__check">{item.icon}</span>
						<span>{item.label}</span>
					</li>
				{/each}
			</ul>
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- FLUXO DE ALTA PERFORMANCE                   -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{fabrica.flux.sectionTitle}</h2>
			{#if fabrica.flux.subtitle}
				<p class="page-section__subtitle">{fabrica.flux.subtitle}</p>
			{/if}
		</div>
		<div class="page-section__grid page-section__grid--3col">
			{#each fabrica.flux.steps as step}
				<ProcessStep
					number={step.number}
					title={step.title}
					description={step.description}
				/>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- CTA FINAL                                   -->
<!-- ═══════════════════════════════════════════ -->
<PageCTA
	title={fabrica.cta.title}
	text={fabrica.cta.text}
	buttonLabel={site.whatsapp.label}
	whatsappUrl={whatsappUrl}
	buttonIcon={fabrica.cta.buttonIcon}
/>

<style>
	/* ── Shared Section Layout ── */
	.page-section {
		padding: 80px var(--spacing-margin-mobile);
	}

	.page-section__inner {
		max-width: var(--spacing-container-max);
		margin: 0 auto;
	}

	.page-section__header {
		text-align: center;
		margin-bottom: 48px;
		display: flex;
		flex-direction: column;
		gap: 8px;
	}

	.page-section__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg-mobile);
		font-weight: var(--font-weight-headline-lg);
		color: var(--color-on-surface);
	}

	.page-section__subtitle {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
	}

	.page-section__grid {
		display: grid;
		gap: 24px;
	}

	@media (min-width: 768px) {
		.page-section__title {
			font-size: var(--font-size-headline-lg);
		}

		.page-section__grid--2col {
			grid-template-columns: repeat(2, 1fr);
		}

		.page-section__grid--3col {
			grid-template-columns: repeat(3, 1fr);
		}
	}

	/* ── Fusion Card ── */
	.fusion-card {
		padding: 40px 32px;
		border-radius: var(--radius-xl);
		border: 1px solid rgba(0, 224, 255, 0.15);
		display: flex;
		flex-direction: column;
		gap: 24px;
	}

	.fusion-card__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-headline-md);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-md);
	}

	.fusion-card__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-lg);
	}

	.fusion-card__list {
		list-style: none;
		display: flex;
		flex-direction: column;
		gap: 14px;
		padding-top: 8px;
		border-top: 1px solid rgba(0, 224, 255, 0.08);
	}

	.fusion-card__item {
		display: flex;
		align-items: center;
		gap: 12px;
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.85;
	}

	.fusion-card__check {
		color: var(--color-primary-container);
		font-size: 20px;
		font-variation-settings: 'FILL' 1;
		flex-shrink: 0;
	}
</style>
