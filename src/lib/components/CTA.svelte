<script lang="ts">
	import site from '$lib/data/site.json';

	const whatsappUrl = `https://wa.me/${site.whatsapp.phone}?text=${encodeURIComponent(site.whatsapp.message)}`;
</script>

<section class="cta" id="contato">
	<div class="cta__card glass-panel">
		<h2 class="cta__title">Pronto para liderar a revolução no seu setor?</h2>
		<p class="cta__text">Nossa equipe de especialistas está pronta para diagnosticar sua infraestrutura atual.</p>
		<!-- svelte-ignore a11y_invalid_attribute -->
		<a class="cta__button" href={whatsappUrl} target="_blank" rel="noopener noreferrer">
			<span class="material-symbols-outlined">chat</span>
			{site.whatsapp.label}
		</a>
	</div>
</section>

<style>
	.cta {
		padding: 96px var(--spacing-margin-mobile);
		text-align: center;
	}

	.cta__card {
		max-width: 42rem;
		margin: 0 auto;
		padding: 40px;
		border-radius: var(--radius-lg);
		border-top: 4px solid var(--color-primary);
		box-shadow: var(--shadow-whatsapp);
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 16px;
	}

	.cta__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		color: var(--color-on-surface);
		margin-bottom: 8px;
	}

	.cta__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		margin-bottom: 16px;
	}

	.cta__button {
		display: inline-flex;
		align-items: center;
		gap: 12px;
		background: var(--color-whatsapp);
		color: var(--color-on-whatsapp);
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		padding: 20px 32px;
		border-radius: var(--radius-sm);
		box-shadow: var(--shadow-whatsapp);
		transition: transform 0.3s;
		text-decoration: none;
	}

	.cta__button:hover {
		transform: scale(1.05);
	}

	.cta__button:active {
		transform: scale(0.95);
	}
</style>
