<section class="solutions">
	<div class="solutions__inner">
		<div class="solutions__header">
			<h2 class="solutions__title">Soluções Deep Tech</h2>
			<p class="solutions__subtitle">Ecossistema completo de automação e inteligência.</p>
		</div>
		<div class="solutions__grid">
			<div class="solutions__card glass-panel">
				<div class="solutions__card-icon-wrapper">
					<span class="material-symbols-outlined solutions__card-icon">web_asset</span>
				</div>
				<div class="solutions__card-body">
					<h3 class="solutions__card-title">Sistemas &amp; Sites de IA</h3>
					<p class="solutions__card-text">Plataformas robustas e personalizadas projetadas para escala global e performance máxima.</p>
				</div>
				<div class="solutions__card-footer">
					<span>Learn more</span>
					<span class="material-symbols-outlined">arrow_forward_ios</span>
				</div>
			</div>

			<div class="solutions__card glass-panel">
				<div class="solutions__card-glow"></div>
				<div class="solutions__card-icon-wrapper">
					<span class="material-symbols-outlined solutions__card-icon">smart_toy</span>
				</div>
				<div class="solutions__card-body">
					<h3 class="solutions__card-title">Agentes &amp; Automação</h3>
					<p class="solutions__card-text">Chatbots inteligentes trabalhando 24/7 para sua gestão, suporte e vendas automáticas.</p>
				</div>
				<div class="solutions__card-footer">
					<span class="solutions__card-active">
						<span class="solutions__card-dot"></span>
						Active
					</span>
					<span class="material-symbols-outlined">arrow_forward_ios</span>
				</div>
			</div>

			<div class="solutions__card glass-panel">
				<div class="solutions__card-icon-wrapper">
					<span class="material-symbols-outlined solutions__card-icon">video_settings</span>
				</div>
				<div class="solutions__card-body">
					<h3 class="solutions__card-title">Fábrica de Mídia IA</h3>
					<p class="solutions__card-text">E-books, Audiobooks, Podcasts e Vídeos Dark automatizados com qualidade cinematográfica.</p>
				</div>
				<div class="solutions__card-footer">
					<span>Automated Production</span>
					<span class="material-symbols-outlined">arrow_forward_ios</span>
				</div>
			</div>
		</div>
	</div>
</section>

<style>
	.solutions {
		padding: 96px var(--spacing-margin-mobile);
	}

	.solutions__inner {
		max-width: var(--spacing-container-max);
		margin: 0 auto;
	}

	.solutions__header {
		text-align: center;
		margin-bottom: 64px;
		display: flex;
		flex-direction: column;
		gap: 16px;
	}

	.solutions__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
	}

	.solutions__subtitle {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
	}

	.solutions__grid {
		display: grid;
		gap: var(--spacing-gutter);
	}

	@media (min-width: 768px) {
		.solutions__grid {
			grid-template-columns: repeat(3, 1fr);
		}
	}

	.solutions__card {
		display: flex;
		flex-direction: column;
		gap: 24px;
		padding: 32px;
		border-radius: var(--radius-lg);
		border: 1px solid rgba(186, 242, 255, 0.1);
		position: relative;
		overflow: hidden;
		transition: border-color 0.5s;
	}

	.solutions__card:hover {
		border-color: rgba(186, 242, 255, 0.5);
	}

	.solutions__card-glow {
		position: absolute;
		right: -48px;
		top: -48px;
		width: 128px;
		height: 128px;
		background: rgba(186, 242, 255, 0.05);
		border-radius: var(--radius-full);
		filter: blur(32px);
	}

	.solutions__card-icon-wrapper {
		width: 64px;
		height: 64px;
		border-radius: var(--radius-sm);
		background: rgba(186, 242, 255, 0.1);
		display: flex;
		align-items: center;
		justify-content: center;
		color: var(--color-primary);
		transition: transform 0.3s;
	}

	.solutions__card:hover .solutions__card-icon-wrapper {
		transform: scale(1.1);
	}

	.solutions__card-icon {
		font-size: 36px;
	}

	.solutions__card-body {
		display: flex;
		flex-direction: column;
		gap: 12px;
	}

	.solutions__card-title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		color: var(--color-on-surface);
		font-weight: var(--font-weight-headline-md);
	}

	.solutions__card-text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.8;
		line-height: var(--line-height-body-md);
	}

	.solutions__card-footer {
		margin-top: auto;
		padding-top: 24px;
		border-top: 1px solid rgba(59, 73, 76, 0.1);
		display: flex;
		justify-content: space-between;
		align-items: center;
		color: var(--color-primary);
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		text-transform: uppercase;
		letter-spacing: 0.1em;
	}

	.solutions__card-active {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.solutions__card-dot {
		width: 8px;
		height: 8px;
		border-radius: var(--radius-full);
		background: var(--color-primary);
		animation: pulse 2s infinite;
	}
</style>
