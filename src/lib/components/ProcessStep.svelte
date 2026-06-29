<script lang="ts">
	let {
		number = '01',
		title = '',
		description = ''
	}: {
		number?: string;
		title?: string;
		description?: string;
	} = $props();
</script>

<div class="process-step">
	<div class="process-step__number">{number}</div>
	<div class="process-step__content">
		<h3 class="process-step__title">{title}</h3>
		{#if description}
			<p class="process-step__text">{description}</p>
		{/if}
	</div>
</div>

<style>
	.process-step {
		display: flex;
		flex-direction: column;
		gap: 16px;
		padding: 28px 24px;
		border-radius: var(--radius-md);
		border: 1px solid rgba(0, 224, 255, 0.1);
		background: rgba(12, 19, 36, 0.5);
		position: relative;
		overflow: hidden;
	}

	.process-step::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		height: 3px;
		background: linear-gradient(90deg, transparent, var(--color-primary-container), transparent);
	}

	.process-step__number {
		font-family: var(--font-display);
		font-size: var(--font-size-display-lg);
		font-weight: var(--font-weight-bold);
		color: var(--color-primary-container);
		opacity: 0.15;
		line-height: 1;
	}

	.process-step__content {
		display: flex;
		flex-direction: column;
		gap: 8px;
	}

	.process-step__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-headline-md);
		color: var(--color-on-surface);
	}

	.process-step__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-md);
	}
</style>
