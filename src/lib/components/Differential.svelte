<section class="differential">
	<div class="differential__card glass-panel">
		<div class="differential__icon-wrapper">
			<span class="material-symbols-outlined differential__icon">verified_user</span>
		</div>
		<h2 class="differential__title">Alta Tecnologia não precisa ser cara.</h2>
		<p class="differential__text">
			Quebramos a barreira do preço. Democratizamos o acesso à Deep Tech com soluções ultra avançadas por valores extremamente acessíveis.
		</p>
		<div class="differential__tags">
			<span class="differential__tag">
				<span class="material-symbols-outlined differential__tag-icon">check_circle</span>
				COST-EFFICIENT
			</span>
			<span class="differential__tag">
				<span class="material-symbols-outlined differential__tag-icon">check_circle</span>
				SCALABLE ARCHITECTURE
			</span>
			<span class="differential__tag">
				<span class="material-symbols-outlined differential__tag-icon">check_circle</span>
				AI-FIRST APPROACH
			</span>
		</div>
	</div>
</section>

<style>
	.differential {
		position: relative;
		padding: 96px var(--spacing-margin-mobile);
		overflow: hidden;
	}

	.differential__card {
		position: relative;
		z-index: 10;
		max-width: 56rem;
		margin: 0 auto;
		text-align: center;
		padding: 48px;
		border-radius: var(--radius-xl);
		border: 1px solid rgba(186, 242, 255, 0.2);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 24px;
	}

	.differential__icon-wrapper {
		margin-bottom: 24px;
	}

	.differential__icon {
		color: var(--color-primary);
		font-size: 48px;
		font-variation-settings: 'FILL' 1;
	}

	.differential__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
	}

	.differential__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-lg);
		color: var(--color-on-surface);
		opacity: 0.9;
		max-width: 42rem;
		margin: 0 auto;
		line-height: var(--line-height-body-lg);
		margin-bottom: 16px;
	}

	.differential__tags {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		gap: 24px;
	}

	.differential__tag {
		display: flex;
		align-items: center;
		gap: 8px;
		color: var(--color-primary);
		font-family: var(--font-code);
		font-size: var(--font-size-code-md);
	}

	.differential__tag-icon {
		font-size: 20px;
	}
</style>
