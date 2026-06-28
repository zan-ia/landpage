<script lang="ts">
	import site from '$lib/data/site.json';
	import sistemas from '$lib/data/sistemas.json';
	import PageHero from '$lib/components/PageHero.svelte';
	import FeatureCard from '$lib/components/FeatureCard.svelte';
	import ProcessStep from '$lib/components/ProcessStep.svelte';
	import PageCTA from '$lib/components/PageCTA.svelte';

	const whatsappUrl = `https://wa.me/${site.whatsapp.phone}?text=${encodeURIComponent(site.whatsapp.message)}`;
</script>

<svelte:head>
	<title>Sistemas & Sites de IA | ZAN.IA</title>
	<meta name="description" content="Sistemas e sites de alta performance com IA integrada. Arquitetura escalável, performance extrema e stack next-gen para escala global." />
</svelte:head>

<!-- ═══════════════════════════════════════════ -->
<!-- HERO                                        -->
<!-- ═══════════════════════════════════════════ -->
<PageHero
	badgeIcon={sistemas.hero.badgeIcon}
	badgeText={sistemas.hero.badgeText}
	title={sistemas.hero.title}
	highlight={sistemas.hero.highlight}
	subtitle={sistemas.hero.subtitle}
	primaryCtaLabel={sistemas.hero.primaryCta.label}
	primaryCtaHref={whatsappUrl}
	primaryCtaIcon={sistemas.hero.primaryCta.icon}
	secondaryCtaLabel={sistemas.hero.secondaryCta.label}
	secondaryCtaHref={sistemas.hero.secondaryCta.href}
	secondaryCtaIcon={sistemas.hero.secondaryCta.icon}
	bgGradient="right"
/>

<!-- ═══════════════════════════════════════════ -->
<!-- EXCELÊNCIA EM ENGENHARIA DIGITAL            -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{sistemas.engineering.sectionTitle}</h2>
		</div>
		<div class="page-section__grid page-section__grid--2col">
			{#each sistemas.engineering.cards as card}
				<FeatureCard
					icon={card.icon}
					title={card.title}
					description={card.description}
					tags={card.tags ?? []}
					metrics={card.metrics ?? []}
					featured={card.featured ?? false}
				/>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- STACK TECNOLÓGICA                           -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{sistemas.stack.sectionTitle}</h2>
			{#if sistemas.stack.subtitle}
				<p class="page-section__subtitle">{sistemas.stack.subtitle}</p>
			{/if}
		</div>
		<div class="page-section__grid page-section__grid--3col">
			{#each sistemas.stack.cards as card}
				<div class="stack-card glass-panel-light">
					<div class="stack-card__icon-wrapper">
						<span class="material-symbols-outlined stack-card__icon">{card.icon}</span>
					</div>
					<h3 class="stack-card__title">{card.title}</h3>
					<p class="stack-card__text">{card.description}</p>
				</div>
			{/each}
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- TERMINAL STATUS                             -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="terminal glass-panel">
		<div class="terminal__header">
			<div class="terminal__dots">
				<span class="terminal__dot terminal__dot--red"></span>
				<span class="terminal__dot terminal__dot--yellow"></span>
				<span class="terminal__dot terminal__dot--green"></span>
			</div>
			<span class="terminal__title">{sistemas.terminal.title}</span>
		</div>
		<div class="terminal__body">
			{#each sistemas.terminal.lines as line}
				<div class="terminal__line terminal__line--{line.type}">
					<span class="terminal__prompt">{line.prompt}</span>
					<span>{line.text}</span>
				</div>
			{/each}
			<div class="terminal__status">
				<span class="terminal__status-badge">
					<span class="terminal__status-dot"></span>
					{sistemas.terminal.status}
				</span>
			</div>
		</div>
	</div>
</section>

<!-- ═══════════════════════════════════════════ -->
<!-- PROCESSO DE DESENVOLVIMENTO                 -->
<!-- ═══════════════════════════════════════════ -->
<section class="page-section">
	<div class="page-section__inner">
		<div class="page-section__header">
			<h2 class="page-section__title">{sistemas.process.sectionTitle}</h2>
			{#if sistemas.process.subtitle}
				<p class="page-section__subtitle">{sistemas.process.subtitle}</p>
			{/if}
		</div>
		<div class="page-section__grid page-section__grid--4col">
			{#each sistemas.process.steps as step}
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
	title={sistemas.cta.title}
	text={sistemas.cta.text}
	buttonLabel={site.whatsapp.label}
	whatsappUrl={whatsappUrl}
	buttonIcon={sistemas.cta.buttonIcon}
	responseTime={sistemas.cta.responseTime}
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
		gap: 12px;
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
		max-width: 36rem;
		margin: 0 auto;
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

		.page-section__grid--4col {
			grid-template-columns: repeat(4, 1fr);
		}
	}

	/* ── Stack Card ── */
	.stack-card {
		padding: 32px;
		border-radius: var(--radius-lg);
		display: flex;
		flex-direction: column;
		gap: 16px;
		text-align: center;
		transition: transform 0.3s;
	}

	.stack-card:hover {
		transform: translateY(-4px);
	}

	.stack-card__icon-wrapper {
		width: 64px;
		height: 64px;
		border-radius: var(--radius-md);
		background: rgba(0, 224, 255, 0.1);
		display: flex;
		align-items: center;
		justify-content: center;
		margin: 0 auto;
		color: var(--color-primary-container);
	}

	.stack-card__icon {
		font-size: 36px;
	}

	.stack-card__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-headline-md);
		color: var(--color-on-surface);
	}

	.stack-card__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-md);
	}

	/* ── Terminal ── */
	.terminal {
		max-width: 42rem;
		margin: 0 auto;
		border-radius: var(--radius-md);
		border: 1px solid rgba(0, 224, 255, 0.15);
		overflow: hidden;
	}

	.terminal__header {
		display: flex;
		align-items: center;
		gap: 12px;
		padding: 12px 16px;
		background: rgba(0, 224, 255, 0.05);
		border-bottom: 1px solid rgba(0, 224, 255, 0.08);
	}

	.terminal__dots {
		display: flex;
		gap: 6px;
	}

	.terminal__dot {
		width: 10px;
		height: 10px;
		border-radius: var(--radius-full);
	}

	.terminal__dot--red { background: var(--color-terminal-red); }
	.terminal__dot--yellow { background: var(--color-terminal-yellow); }
	.terminal__dot--green { background: var(--color-terminal-green); }

	.terminal__title {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.4;
	}

	.terminal__body {
		padding: 20px 24px;
		display: flex;
		flex-direction: column;
		gap: 10px;
	}

	.terminal__line {
		display: flex;
		gap: 12px;
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
	}

	.terminal__prompt {
		flex-shrink: 0;
		width: 20px;
		text-align: center;
	}

	.terminal__line--success { color: var(--color-terminal-green); }
	.terminal__line--info { color: var(--color-primary-container); }
	.terminal__line--warn { color: var(--color-terminal-yellow); }
	.terminal__line--muted { color: var(--color-on-surface); opacity: 0.4; }

	.terminal__status {
		margin-top: 8px;
		padding-top: 12px;
		border-top: 1px solid rgba(0, 224, 255, 0.08);
	}

	.terminal__status-badge {
		display: inline-flex;
		align-items: center;
		gap: 8px;
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-primary-container);
	}

	.terminal__status-dot {
		width: 8px;
		height: 8px;
		border-radius: var(--radius-full);
		background: var(--color-terminal-green);
		animation: pulse-glow 2s ease-in-out infinite;
	}

	@keyframes pulse-glow {
		0%, 100% { box-shadow: 0 0 0 0 rgba(39, 201, 63, 0.4); }
		50% { box-shadow: 0 0 0 6px rgba(39, 201, 63, 0); }
	}
</style>
