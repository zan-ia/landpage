<script lang="ts">
	let {
		title = '',
		text = '',
		buttonLabel = '',
		whatsappUrl = '',
		buttonIcon = 'chat',
		responseTime = ''
	}: {
		title?: string;
		text?: string;
		buttonLabel?: string;
		whatsappUrl?: string;
		buttonIcon?: string;
		responseTime?: string;
	} = $props();
</script>

<section class="page-cta" id="contato">
	<div class="page-cta__card glass-panel">
		<h2 class="page-cta__title">{title}</h2>
		{#if text}
			<p class="page-cta__text">{text}</p>
		{/if}
		{#if buttonLabel && whatsappUrl}
			<a class="page-cta__button" href={whatsappUrl} target="_blank" rel="noopener noreferrer">
				<span class="material-symbols-outlined">{buttonIcon}</span>
				{buttonLabel}
			</a>
		{/if}
		{#if responseTime}
			<p class="page-cta__response-time">{responseTime}</p>
		{/if}
	</div>
</section>

<style>
	.page-cta {
		padding: 80px var(--spacing-margin-mobile);
		text-align: center;
	}

	.page-cta__card {
		max-width: 36rem;
		margin: 0 auto;
		padding: 48px 32px;
		border-radius: var(--radius-xl);
		border-top: 4px solid var(--color-primary-container);
		box-shadow: var(--shadow-glow-primary);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 16px;
	}

	.page-cta__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg-mobile);
		font-weight: var(--font-weight-headline-lg);
		color: var(--color-on-surface);
		line-height: var(--line-height-headline-lg);
	}

	.page-cta__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-md);
	}

	.page-cta__button {
		display: inline-flex;
		align-items: center;
		gap: 12px;
		background: var(--color-whatsapp);
		color: var(--color-on-whatsapp);
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-semibold);
		padding: 18px 32px;
		border-radius: var(--radius-sm);
		text-decoration: none;
		transition: transform 0.2s, box-shadow 0.3s;
		box-shadow: var(--shadow-whatsapp);
	}

	.page-cta__button:hover {
		transform: scale(1.04);
	}

	.page-cta__response-time {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.4;
	}

	@media (min-width: 768px) {
		.page-cta__title {
			font-size: var(--font-size-headline-lg);
		}
	}
</style>
