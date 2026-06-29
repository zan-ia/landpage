<script lang="ts">
	let {
		icon = 'smart_toy',
		title = '',
		description = '',
		tags = [] as string[],
		features = [] as string[],
		metrics = [] as { value: string; unit?: string; label: string }[],
		featured = false,
		href = ''
	}: {
		icon?: string;
		title?: string;
		description?: string;
		tags?: string[];
		features?: string[];
		metrics?: { value: string; unit?: string; label: string }[];
		featured?: boolean;
		href?: string;
	} = $props();

	const cardClasses = $derived([
		'feature-card',
		'glass-panel',
		featured ? 'feature-card--featured' : ''
	].filter(Boolean).join(' '));
</script>

{#if href}
	<a {href} class={cardClasses}>
		{#if featured}<div class="feature-card__glow"></div>{/if}
		<div class="feature-card__icon-wrapper">
			<span class="material-symbols-outlined feature-card__icon">{icon}</span>
		</div>
		<div class="feature-card__body">
			<h3 class="feature-card__title">{title}</h3>
			{#if description}<p class="feature-card__text">{description}</p>{/if}
		</div>
		{#if tags.length > 0}
			<div class="feature-card__tags">
				{#each tags as tag}
					<span class="feature-card__tag">{tag}</span>
				{/each}
			</div>
		{/if}
		{#if features.length > 0}
			<ul class="feature-card__features">
				{#each features as feature}
					<li class="feature-card__feature">
						<span class="material-symbols-outlined feature-card__check">check_circle</span>
						{feature}
					</li>
				{/each}
			</ul>
		{/if}
		{#if metrics.length > 0}
			<div class="feature-card__metrics">
				{#each metrics as metric}
					<div class="feature-card__metric">
						<span class="feature-card__metric-value">
							{metric.value}
							{#if metric.unit}<span class="feature-card__metric-unit">{metric.unit}</span>{/if}
						</span>
						<span class="feature-card__metric-label">{metric.label}</span>
					</div>
				{/each}
			</div>
		{/if}
	</a>
{:else}
	<div class={cardClasses}>
		{#if featured}<div class="feature-card__glow"></div>{/if}
		<div class="feature-card__icon-wrapper">
			<span class="material-symbols-outlined feature-card__icon">{icon}</span>
		</div>
		<div class="feature-card__body">
			<h3 class="feature-card__title">{title}</h3>
			{#if description}<p class="feature-card__text">{description}</p>{/if}
		</div>
		{#if tags.length > 0}
			<div class="feature-card__tags">
				{#each tags as tag}
					<span class="feature-card__tag">{tag}</span>
				{/each}
			</div>
		{/if}
		{#if features.length > 0}
			<ul class="feature-card__features">
				{#each features as feature}
					<li class="feature-card__feature">
						<span class="material-symbols-outlined feature-card__check">check_circle</span>
						{feature}
					</li>
				{/each}
			</ul>
		{/if}
		{#if metrics.length > 0}
			<div class="feature-card__metrics">
				{#each metrics as metric}
					<div class="feature-card__metric">
						<span class="feature-card__metric-value">
							{metric.value}
							{#if metric.unit}<span class="feature-card__metric-unit">{metric.unit}</span>{/if}
						</span>
						<span class="feature-card__metric-label">{metric.label}</span>
					</div>
				{/each}
			</div>
		{/if}
	</div>
{/if}

<style>
	.feature-card {
		display: flex;
		flex-direction: column;
		gap: 20px;
		padding: 32px;
		border-radius: var(--radius-lg);
		border: 1px solid rgba(0, 224, 255, 0.1);
		position: relative;
		overflow: hidden;
		transition: border-color 0.4s, transform 0.3s;
		text-decoration: none;
		color: inherit;
	}

	.feature-card:hover {
		border-color: rgba(0, 224, 255, 0.4);
		transform: translateY(-4px);
	}

	.feature-card--featured {
		border-color: rgba(0, 224, 255, 0.2);
	}

	.feature-card__glow {
		position: absolute;
		right: -64px;
		top: -64px;
		width: 180px;
		height: 180px;
		background: rgba(0, 224, 255, 0.06);
		border-radius: var(--radius-full);
		filter: blur(48px);
		pointer-events: none;
	}

	.feature-card__icon-wrapper {
		width: 56px;
		height: 56px;
		border-radius: var(--radius-sm);
		background: rgba(0, 224, 255, 0.1);
		display: flex;
		align-items: center;
		justify-content: center;
		color: var(--color-primary-container);
	}

	.feature-card__icon {
		font-size: 32px;
	}

	.feature-card__body {
		display: flex;
		flex-direction: column;
		gap: 8px;
	}

	.feature-card__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-headline-md);
		color: var(--color-on-surface);
	}

	.feature-card__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
		line-height: var(--line-height-body-md);
	}

	.feature-card__tags {
		display: flex;
		flex-wrap: wrap;
		gap: 8px;
		padding-top: 4px;
	}

	.feature-card__tag {
		padding: 4px 12px;
		border-radius: var(--radius-full);
		border: 1px solid rgba(0, 224, 255, 0.2);
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-primary-container);
	}

	.feature-card__features {
		list-style: none;
		display: flex;
		flex-direction: column;
		gap: 10px;
		padding-top: 8px;
		border-top: 1px solid rgba(0, 224, 255, 0.08);
	}

	.feature-card__feature {
		display: flex;
		align-items: center;
		gap: 8px;
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.8;
	}

	.feature-card__check {
		color: var(--color-primary-container);
		font-size: 16px;
		font-variation-settings: 'FILL' 1;
	}

	.feature-card__metrics {
		display: flex;
		gap: 32px;
		justify-content: space-around;
		padding-top: 8px;
		border-top: 1px solid rgba(0, 224, 255, 0.1);
	}

	.feature-card__metric {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 4px;
	}

	.feature-card__metric-value {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		font-weight: var(--font-weight-bold);
		color: var(--color-primary-container);
	}

	.feature-card__metric-unit {
		font-size: var(--font-size-body-md);
		opacity: 0.6;
	}

	.feature-card__metric-label {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.5;
		letter-spacing: var(--letter-spacing-label-sm);
	}
</style>
