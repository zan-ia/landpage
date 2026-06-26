<script lang="ts">
	import { base } from '$app/paths';
</script>

<section class="hero">
	<div class="hero__bg">
		<div class="hero__overlay"></div>
		<img
			src="{base}/assets/images/cover.png"
			fetchpriority="high"
			alt="Hero background - Deep Tech visual abstract"
			class="hero__image"
		/>
	</div>
	<div class="hero__content">
		<div class="hero__badge">
			<span class="material-symbols-outlined hero__badge-icon">bolt</span>
			<span class="hero__badge-text">Deep Tech Revolution</span>
		</div>
		<h1 class="hero__title">
			Tecnologia de Ponta e Conteúdo de Elite. <br class="hide-mobile" />
			<span class="hero__highlight">Agora ao Seu Alcance.</span>
		</h1>
		<p class="hero__description">
			Unimos a experiência de veteranos da indústria à inovação da IA para criar sistemas, automações e mídias integradas com custo-benefício imbatível.
		</p>
		<div class="hero__cta-wrapper">
			<button class="hero__cta">
				Transformar meu Negócio
				<span class="material-symbols-outlined hero__cta-icon">trending_up</span>
			</button>
		</div>
	</div>
	<div class="hero__discovery">
		<span class="hero__discovery-label">Discovery</span>
		<div class="hero__discovery-line"></div>
	</div>
</section>

<style>
	.hero {
		position: relative;
		min-height: 90vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		text-align: center;
		padding-left: var(--spacing-margin-mobile);
		padding-right: var(--spacing-margin-mobile);
		overflow: hidden;
	}

	.hero__bg {
		position: absolute;
		inset: 0;
		z-index: 0;
		opacity: 0.4;
	}

	.hero__overlay {
		position: absolute;
		inset: 0;
		background: linear-gradient(to bottom, var(--color-surface), transparent, var(--color-surface));
		z-index: 10;
	}

	.hero__image {
		width: 100%;
		height: 100%;
		object-fit: cover;
	}

	.hero__content {
		position: relative;
		z-index: 20;
		max-width: 56rem;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		gap: 24px;
	}

	.hero__badge {
		display: inline-flex;
		align-items: center;
		gap: 8px;
		padding: 4px 12px;
		border-radius: var(--radius-full);
		border: 1px solid rgba(186, 242, 255, 0.3);
		background: rgba(186, 242, 255, 0.1);
		color: var(--color-primary-fixed-dim);
		margin: 0 auto;
	}

	.hero__badge-icon {
		font-size: 14px;
		font-variation-settings: 'FILL' 1;
	}

	.hero__badge-text {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		text-transform: uppercase;
		letter-spacing: 0.1em;
	}

	.hero__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg-mobile);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
	}

	@media (min-width: 768px) {
		.hero__title {
			font-size: var(--font-size-headline-lg);
		}
	}

	.hero__highlight {
		color: var(--color-primary-container);
	}

	.hero__description {
		font-family: var(--font-body);
		font-size: var(--font-size-body-lg);
		color: var(--color-on-surface);
		opacity: 0.8;
		max-width: 42rem;
		margin: 0 auto;
		line-height: var(--line-height-body-lg);
	}

	.hero__cta-wrapper {
		padding-top: 32px;
	}

	.hero__cta {
		display: inline-flex;
		align-items: center;
		gap: 12px;
		background: var(--color-primary-container);
		color: var(--color-on-primary-container);
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: 700;
		padding: 16px 32px;
		border-radius: var(--radius-md);
		box-shadow: var(--shadow-glow-primary);
		transition: box-shadow 0.3s, transform 0.15s;
	}

	.hero__cta:hover {
		box-shadow: var(--shadow-glow-primary-hover);
	}

	.hero__cta:active {
		transform: scale(0.95);
	}

	.hero__cta-icon {
		transition: transform 0.3s;
	}

	.hero__cta:hover .hero__cta-icon {
		transform: translateX(4px);
	}

	.hero__discovery {
		position: absolute;
		bottom: 32px;
		left: 50%;
		transform: translateX(-50%);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8px;
		opacity: 0.4;
	}

	.hero__discovery-label {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		text-transform: uppercase;
		letter-spacing: 0.2em;
	}

	.hero__discovery-line {
		width: 1px;
		height: 48px;
		background: linear-gradient(to bottom, var(--color-primary), transparent);
	}
</style>
