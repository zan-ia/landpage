<script lang="ts">
	import site from '$lib/data/site.json';
	import agentes from '$lib/data/agentes.json';
	import PageHero from '$lib/components/PageHero.svelte';
	import FeatureCard from '$lib/components/FeatureCard.svelte';
	import PageCTA from '$lib/components/PageCTA.svelte';

	const whatsappUrl = `https://wa.me/${site.whatsapp.phone}?text=${encodeURIComponent(site.whatsapp.message)}`;
</script>

<svelte:head>
	<title>Agentes & Automação | ZAN.IA</title>
	<meta name="description" content="Agentes e automação de próxima geração. Força de trabalho digital inteligente 24/7 para atendimento, vendas e gestão operacional com IA preditiva." />
</svelte:head>

<!-- ═══════════════════════════════════════════ -->
<!-- HERO                                        -->
<!-- ═══════════════════════════════════════════ -->
<PageHero
	badgeIcon={agentes.hero.badgeIcon}
	badgeText={agentes.hero.badgeText}
	title={agentes.hero.title}
	highlight={agentes.hero.highlight}
	subtitle={agentes.hero.subtitle}
	primaryCtaLabel={agentes.hero.primaryCta.label}
	primaryCtaHref={whatsappUrl}
	primaryCtaIcon={agentes.hero.primaryCta.icon}
	secondaryCtaLabel={agentes.hero.secondaryCta.label}
	secondaryCtaHref={agentes.hero.secondaryCta.href}
	secondaryCtaIcon={agentes.hero.secondaryCta.icon}
/>

<!-- ═══════════════════════════════════════════ -->
<!-- CAPACIDADES COGNITIVAS                      -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section" id="capacidades">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{agentes.capabilities.sectionTitle}</h2>
		</div>
		<div class="page-section__grid page-section__grid--3col">
			{#each agentes.capabilities.cards as card}
				<FeatureCard
					icon={card.icon}
					title={card.title}
					description={card.description}
					features={card.features ?? []}
					featured={card.featured ?? false}
				/>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- ARQUITETURA DE ALTA PERFORMANCE             -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{agentes.performance.sectionTitle}</h2>
		</div>
		<div class="page-section__grid page-section__grid--2col page-section__grid--4col-desktop">
			{#each agentes.performance.metrics as metric}
				<div class="metric-card glass-panel-light">
					<span class="metric-card__value">
						{metric.value}
						{#if metric.unit}
							<span class="metric-card__unit">{metric.unit}</span>
						{/if}
					</span>
					<span class="metric-card__label">{metric.label}</span>
				</div>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- CONECTIVIDADE TOTAL                         -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{agentes.connectivity.sectionTitle}</h2>
			{#if agentes.connectivity.subtitle}
				<p class="page-section__subtitle">{agentes.connectivity.subtitle}</p>
			{/if}
		</div>
		<div class="page-section__grid page-section__grid--2col page-section__grid--4col-desktop">
			{#each agentes.connectivity.items as item}
				<div class="conn-item glass-panel-light">
					<span class="material-symbols-outlined conn-item__icon">{item.icon}</span>
					<span class="conn-item__label">{item.label}</span>
				</div>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- CTA FINAL                                   -->
<!-- ═══════════════════════════════════════════ -->
<PageCTA
	title={agentes.cta.title}
	text={agentes.cta.text}
	buttonLabel={site.whatsapp.label}
	whatsappUrl={whatsappUrl}
	buttonIcon={agentes.cta.buttonIcon}
	responseTime={agentes.cta.responseTime}
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

	.page-section__grid--2col {
		grid-template-columns: repeat(2, 1fr);
	}

	@media (min-width: 768px) {
		.page-section__title {
			font-size: var(--font-size-headline-lg);
		}

		.page-section__grid--3col {
			grid-template-columns: repeat(3, 1fr);
		}

		.page-section__grid--4col-desktop {
			grid-template-columns: repeat(4, 1fr);
		}
	}

	/* ── Metric Card ── */
	.metric-card {
		padding: 28px 20px;
		border-radius: var(--radius-md);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8px;
		text-align: center;
	}

	.metric-card__value {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		font-weight: var(--font-weight-bold);
		color: var(--color-primary-container);
		line-height: 1;
	}

	.metric-card__unit {
		font-size: var(--font-size-body-lg);
		opacity: 0.6;
	}

	.metric-card__label {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.5;
		letter-spacing: var(--letter-spacing-label-sm);
	}

	/* ── Connectivity Item ── */
	.conn-item {
		padding: 28px 20px;
		border-radius: var(--radius-md);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 12px;
		transition: transform 0.3s, border-color 0.3s;
	}

	.conn-item:hover {
		transform: translateY(-2px);
	}

	.conn-item__icon {
		font-size: 32px;
		color: var(--color-primary-container);
	}

	.conn-item__label {
		font-family: var(--font-code);
		font-size: var(--font-size-code-md);
		color: var(--color-on-surface);
		opacity: 0.8;
	}
</style>
