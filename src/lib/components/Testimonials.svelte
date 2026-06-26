<script lang="ts">
	const testimonials = [
		{
			stars: 5,
			text: "A Zan.IA transformou nossa operação. O que levava dias agora é feito em minutos com os agentes de IA. O ROI foi imediato.",
			initials: "RS",
			name: "Roberto S.",
			role: "CEO, TechFlow"
		},
		{
			stars: 5,
			text: "A qualidade da Fábrica de Mídia é impressionante. Conseguimos escalar nossa produção de conteúdo sem perder a essência da marca.",
			initials: "ML",
			name: "Marina L.",
			role: "Diretora de Marketing"
		},
		{
			stars: 5,
			text: "Sistemas robustos e suporte excepcional. A Zan.IA é o parceiro tecnológico que toda empresa moderna precisa ter ao seu lado.",
			initials: "AT",
			name: "André T.",
			role: "CTO, Nexus Corp"
		}
	];

	const total = testimonials.length;
	let carouselEl = $state<HTMLElement>();
	let sectionEl = $state<HTMLElement>();
	let currentIndex = $state(0);
	let isHovering = $state(false);
	let intervalId: ReturnType<typeof setInterval> | undefined;

	// ── Drag state ──
	let isDragging = $state(false);
	let pendingScroll = false;
	let startX = 0;
	let scrollLeftStart = 0;

	/** Largura de um card + gap */
	function cardStep(): number {
		if (!carouselEl) return 474;
		const card = carouselEl.querySelector('.testimonial__card');
		if (!card) return 474;
		return card.clientWidth + 24;
	}

	/** Ir para um card específico (pelo índice real 0..total-1) */
	function goTo(index: number) {
		if (!carouselEl) return;
		pendingScroll = true;
		const step = cardStep();
		const max = carouselEl.scrollWidth - carouselEl.clientWidth;
		const target = Math.min(index * step, max);
		carouselEl.scrollTo({ left: target, behavior: 'smooth' });
		currentIndex = index;
	}

	/** Avança para o próximo card (com wrap instantâneo) */
	function goNext() {
		if (!carouselEl) return;
		const nextIndex = (currentIndex + 1) % total;
		goTo(nextIndex);
	}

	// ── Drag via Pointer Events ──
	function onPointerDown(e: PointerEvent) {
		if (!carouselEl || e.button !== 0) return;
		isDragging = true;
		startX = e.clientX;
		scrollLeftStart = carouselEl.scrollLeft;
		carouselEl.setPointerCapture(e.pointerId);
		carouselEl.style.cursor = 'grabbing';
		carouselEl.style.userSelect = 'none';
	}

	function onPointerMove(e: PointerEvent) {
		if (!isDragging || !carouselEl) return;
		e.preventDefault();
		const dx = startX - e.clientX;
		carouselEl.scrollLeft = scrollLeftStart + dx;
	}

	function onPointerUp(_e: PointerEvent) {
		if (!isDragging || !carouselEl) return;
		isDragging = false;
		carouselEl.style.cursor = '';
		carouselEl.style.userSelect = '';
		// Snap ao soltar
		const step = cardStep();
		const idx = Math.round(carouselEl.scrollLeft / step);
		currentIndex = Math.max(0, Math.min(idx, total - 1));
	}

	// ── Tracking do scroll programático ──
	function scrollHandler() {
		if (!carouselEl || isDragging) return;
		const step = cardStep();
		const idx = Math.round(carouselEl.scrollLeft / step);
		currentIndex = Math.max(0, Math.min(idx, total - 1));
	}

	/** No fim do scroll, wrappia se estiver no fim (só em arrasto manual) */
	function onScrollEnd() {
		if (!carouselEl) return;
		if (pendingScroll) {
			pendingScroll = false;
			return;
		}
		const max = carouselEl.scrollWidth - carouselEl.clientWidth;
		if (carouselEl.scrollLeft >= max - 5) {
			carouselEl.scrollTo({ left: 0, behavior: 'instant' });
			currentIndex = 0;
		}
	}

	// ── Auto-play ──
	function startAutoPlay() {
		stopAutoPlay();
		intervalId = setInterval(goNext, 4000);
	}

	function stopAutoPlay() {
		if (intervalId) {
			clearInterval(intervalId);
			intervalId = undefined;
		}
	}

	function onSectionEnter() {
		isHovering = true;
		stopAutoPlay();
	}

	function onSectionLeave() {
		isHovering = false;
		startAutoPlay();
	}

	$effect(() => {
		startAutoPlay();
		return () => stopAutoPlay();
	});
</script>

<section
	class="testimonials"
	role="region"
	aria-label="Depoimentos de parceiros"
	bind:this={sectionEl}
	onmouseenter={onSectionEnter}
	onmouseleave={onSectionLeave}
>
	<div class="testimonials__inner">
		<div class="testimonials__header">
			<h2 class="testimonials__title">O que nossos parceiros dizem</h2>
			<p class="testimonials__subtitle">Histórias de transformação digital impulsionadas por IA.</p>
		</div>
		<div
			class="testimonials__carousel"
			role="group"
			aria-roledescription="carrossel"
			bind:this={carouselEl}
			onpointerdown={onPointerDown}
			onpointermove={onPointerMove}
			onpointerup={onPointerUp}
			onscroll={scrollHandler}
			onscrollend={onScrollEnd}
		>
			{#each testimonials as t, i}
				<div class="testimonial__card glass-panel">
					<div class="testimonial__stars">
						{#each Array(t.stars) as _}
							<span class="material-symbols-outlined testimonial__star">star</span>
						{/each}
					</div>
					<p class="testimonial__text">{t.text}</p>
					<div class="testimonial__author">
						<div class="testimonial__avatar">{t.initials}</div>
						<div>
							<div class="testimonial__name">{t.name}</div>
							<div class="testimonial__role">{t.role}</div>
						</div>
					</div>
				</div>
			{/each}
		</div>
		<div class="testimonials__dots">
			{#each testimonials as _, i}
				<button
					class="testimonials__dot"
					class:testimonials__dot--active={i === currentIndex}
					class:testimonials__dot--pulsing={i === currentIndex && !isHovering}
					onclick={() => goTo(i)}
					aria-label={`Depoimento ${i + 1}`}
				></button>
			{/each}
		</div>
	</div>
</section>

<style>
	.testimonials {
		padding: 96px var(--spacing-margin-mobile);
	}

	.testimonials__inner {
		max-width: var(--spacing-container-max);
		margin: 0 auto;
	}

	.testimonials__header {
		text-align: center;
		margin-bottom: 64px;
		display: flex;
		flex-direction: column;
		gap: 16px;
	}

	.testimonials__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-lg);
		color: var(--color-on-surface);
	}

	.testimonials__subtitle {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.7;
	}

	.testimonials__carousel {
		display: flex;
		overflow-x: auto;
		scroll-behavior: smooth;
		gap: var(--spacing-gutter);
		padding: 0 var(--spacing-margin-mobile) 32px;
		-ms-overflow-style: none;
		scrollbar-width: none;
		cursor: grab;
		touch-action: none;
		-webkit-overflow-scrolling: touch;
	}
	.testimonials__carousel:active {
		cursor: grabbing;
	}
	.testimonials__carousel::-webkit-scrollbar {
		display: none;
	}

	.testimonial__card {
		display: flex;
		flex-direction: column;
		gap: 24px;
		padding: 32px;
		border-radius: var(--radius-lg);
		border: 1px solid rgba(186, 242, 255, 0.1);
		background: rgba(255, 255, 255, 0.03);
		backdrop-filter: blur(24px);
		-webkit-backdrop-filter: blur(24px);
		flex-shrink: 0;
		width: 350px;
		user-select: none;
	}

	@media (min-width: 768px) {
		.testimonial__card {
			width: 450px;
		}
	}

	.testimonial__stars {
		display: flex;
		gap: 4px;
		color: var(--color-primary);
	}

	.testimonial__star {
		font-size: 20px;
		font-variation-settings: 'FILL' 1;
	}

	.testimonial__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.8;
		font-style: italic;
		line-height: 1.625;
		white-space: normal;
	}

	.testimonial__author {
		margin-top: auto;
		display: flex;
		align-items: center;
		gap: 16px;
	}

	.testimonial__avatar {
		width: 48px;
		height: 48px;
		border-radius: var(--radius-full);
		background: rgba(186, 242, 255, 0.2);
		display: flex;
		align-items: center;
		justify-content: center;
		color: var(--color-primary);
		font-weight: 700;
	}

	.testimonial__name {
		font-family: var(--font-display);
		font-size: 16px;
		color: var(--color-on-surface);
	}

	.testimonial__role {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		opacity: 0.6;
	}

	.testimonials__dots {
		display: flex;
		justify-content: center;
		gap: 8px;
		margin-top: 16px;
	}



	.testimonials__dot {
		width: 8px;
		height: 8px;
		border-radius: var(--radius-full);
		background: rgba(186, 242, 255, 0.2);
		transition: background 0.3s, box-shadow 0.3s;
		border: none;
		padding: 0;
		cursor: pointer;
		flex-shrink: 0;
	}

	.testimonials__dot--active {
		background: var(--color-primary);
		box-shadow: 0 0 6px var(--color-primary-container);
	}

	/* ── Auto-play pulse on active dot ── */
	.testimonials__dot--active.testimonials__dot--pulsing {
		animation: dot-pulse 2s ease-in-out infinite;
	}

	@keyframes dot-pulse {
		0%, 100% {
			box-shadow: 0 0 4px var(--color-primary-container);
			transform: scale(1);
		}
		50% {
			box-shadow: 0 0 10px var(--color-primary-container),
						0 0 20px rgba(0, 224, 255, 0.3);
			transform: scale(1.3);
		}
	}
</style>
