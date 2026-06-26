<section class="authority">
	<div class="authority__grid">
		<div class="authority__image-wrapper">
			<div class="authority__glow"></div>
			<div class="authority__card glass-panel">
				<div class="scanning-line"></div>
				<img
					class="authority__image"
					alt="A sophisticated macro shot of high-tech integrated circuits and glowing blue neural pathways inside a glass chamber"
					src="/assets/images/authority-circuit.webp"
					loading="lazy"
				/>
			</div>
		</div>
		<div class="authority__content">
			<div class="authority__header">
				<div class="authority__line"></div>
				<span class="authority__label">Authority</span>
			</div>
			<h2 class="authority__title">O Encontro do Legado com o Futuro</h2>
			<p class="authority__text">
				A Zan.IA é formada por entusiastas e veteranos. Combinamos décadas de bagagem de mercado com a agilidade da nova era tecnológica para entregar resultados reais.
			</p>
			<div class="authority__stats">
				<div class="authority__stat">
					<div class="authority__stat-value">20+</div>
					<div class="authority__stat-label">Anos de Experiência</div>
				</div>
				<div class="authority__stat">
					<div class="authority__stat-value">100%</div>
					<div class="authority__stat-label">IA Nativa</div>
				</div>
			</div>
		</div>
	</div>
</section>

<style>
	.authority {
		padding: 96px var(--spacing-margin-mobile);
		max-width: var(--spacing-container-max);
		margin: 0 auto;
		border-top: 1px solid rgba(59, 73, 76, 0.1);
	}

	.authority__grid {
		display: grid;
		gap: 64px;
		align-items: center;
	}

	@media (min-width: 768px) {
		.authority__grid {
			grid-template-columns: repeat(2, 1fr);
			gap: 64px;
		}
	}

	.authority__image-wrapper {
		position: relative;
	}

	.authority__glow {
		position: absolute;
		inset: -16px;
		background: rgba(186, 242, 255, 0.05);
		border-radius: var(--radius-lg);
		filter: blur(64px);
		transition: background 0.3s;
	}

	.authority__image-wrapper:hover .authority__glow {
		background: rgba(186, 242, 255, 0.1);
	}

	.authority__card {
		position: relative;
		border-radius: var(--radius-lg);
		padding: 16px;
		border: 1px solid rgba(186, 242, 255, 0.1);
		overflow: hidden;
	}

	.authority__image {
		width: 100%;
		aspect-ratio: 1;
		object-fit: cover;
		border-radius: var(--radius-sm);
		filter: grayscale(1);
		transition: filter 0.7s;
	}

	.authority__image-wrapper:hover .authority__image {
		filter: grayscale(0);
	}

	.authority__content {
		display: flex;
		flex-direction: column;
		gap: 24px;
	}

	.authority__header {
		display: flex;
		align-items: center;
		gap: 12px;
	}

	.authority__line {
		height: 4px;
		width: 48px;
		background: var(--color-primary);
		border-radius: var(--radius-full);
	}

	.authority__label {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-primary);
		text-transform: uppercase;
		letter-spacing: 0.3em;
	}

	.authority__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
	}

	.authority__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-lg);
		color: var(--color-on-surface);
		opacity: 0.9;
		line-height: 1.625;
	}

	.authority__stats {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 16px;
		padding-top: 16px;
	}

	.authority__stat {
		background: rgba(15, 23, 42, 0.7);
		backdrop-filter: blur(12px);
		-webkit-backdrop-filter: blur(12px);
		padding: 16px;
		border-radius: var(--radius-sm);
		border: 1px;
		border-left: 2px solid var(--color-primary);
		border-style: solid;
		border-color: var(--color-primary);
		border-top-color: transparent;
		border-right-color: transparent;
		border-bottom-color: transparent;
	}

	.authority__stat-value {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		color: var(--color-primary);
		font-weight: 700;
	}

	.authority__stat-label {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		text-transform: uppercase;
		opacity: 0.6;
	}
</style>
