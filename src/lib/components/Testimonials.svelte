<script lang="ts">
	// ── Tipos ──
	interface Testimonial {
		stars: number;
		text: string;
		initials: string;
		name: string;
		role: string;
	}

	const testimonials: Testimonial[] = [
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
		},
		{
			stars: 5,
			text: "Os agentes de IA da Zan.IA reduziram nosso tempo de resposta ao cliente em 80%. A integração com nossos sistemas foi surpreendentemente rápida.",
			initials: "CG",
			name: "Carla G.",
			role: "Head de CX, Vértice Digital"
		},
		{
			stars: 5,
			text: "Migrar nossa infraestrutura legada para a nuvem com a Zan.IA foi uma das melhores decisões que tomamos. Zero downtime, performance 3x maior.",
			initials: "PF",
			name: "Paulo F.",
			role: "Diretor de TI, Construtora Nova Era"
		},
		{
			stars: 5,
			text: "A Fábrica de Mídia IA produziu 200 variações de criativos em 48 horas. Nossa equipe interna levaria semanas. Resultado absurdo.",
			initials: "LB",
			name: "Luciana B.",
			role: "CMO, Ecom Brands"
		}
	];

	const total = testimonials.length;

	// ── Refs ──
	let carouselEl = $state<HTMLElement>();
	let sectionEl = $state<HTMLElement>();
	let currentIndex = $state(0);
	let isHovering = $state(false);
	let prefersReducedMotion = $state(false);
	let isInViewport = $state(false);
	let autoPlayActive = $state(true);

	// ── Drag state ──
	let isDragging = $state(false);
	let startX = 0;
	let scrollLeftStart = 0;
	let scrollEndTimer: ReturnType<typeof setTimeout> | undefined;
	let scrollEndHandled = false;
	let isProgrammaticScroll = false;
	// Momentum tracking
	let lastMoveX = 0;
	let lastMoveTime = 0;
	let momentumFrame: number | undefined;

	// ── Cached card step (invalidado no resize) ──
	let cachedStep = 0;

	// ── prefers-reduced-motion detection ──
	function checkReducedMotion() {
		prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
	}

	$effect(() => {
		checkReducedMotion();
		const mq = window.matchMedia('(prefers-reduced-motion: reduce)');
		const handler = () => checkReducedMotion();
		mq.addEventListener('change', handler);
		return () => mq.removeEventListener('change', handler);
	});

	// ── IntersectionObserver ──
	$effect(() => {
		if (!sectionEl) return;
		const observer = new IntersectionObserver(
			([entry]) => {
				isInViewport = entry.isIntersecting;
			},
			{ threshold: 0.2 }
		);
		observer.observe(sectionEl);
		return () => observer.disconnect();
	});

	// ── Largura de um card + gap (com cache e ResizeObserver) ──
	function cardStep(): number {
		if (cachedStep > 0) return cachedStep;
		if (!carouselEl) return 474;
		const card = carouselEl.querySelector('.testimonial__card');
		if (!card) return 474;
		const style = getComputedStyle(carouselEl);
		const gap = parseFloat(style.columnGap) || parseFloat(style.gap) || 24;
		cachedStep = card.clientWidth + gap;
		return cachedStep;
	}

	function invalidateStepCache() {
		cachedStep = 0;
	}

	// ── ResizeObserver: recalcula step quando viewport muda ──
	$effect(() => {
		if (!carouselEl) return;
		const card = carouselEl.querySelector('.testimonial__card');
		if (!card) return;

		const observer = new ResizeObserver(() => {
			invalidateStepCache();
			// Re-posiciona no card real atual após resize
			const step = cardStep();
			carouselEl?.scrollTo({ left: (currentIndex + 1) * step, behavior: 'instant' });
		});
		observer.observe(card);
		return () => observer.disconnect();
	});

	// ── Navegação para o card real (índice 1-based: 0 = clone do último, 1..total = reais, total+1 = clone do primeiro) ──
	function goToReal(index: number, smooth = true) {
		if (!carouselEl) return;
		// Não rouba foco de elementos interativos (inputs, textareas, etc.)
		const activeTag = document.activeElement?.tagName;
		if (activeTag === 'INPUT' || activeTag === 'TEXTAREA' || activeTag === 'SELECT') return;
		const step = cardStep();
		// Os cards reais começam na posição 1 (índice 0 é o clone do último)
		const scrollTarget = (index + 1) * step;
		isProgrammaticScroll = true;
		carouselEl.scrollTo({ left: scrollTarget, behavior: smooth ? 'smooth' : 'instant' });
		currentIndex = index;
		// Limpa a flag após a animação de scroll terminar (~500ms para smooth)
		setTimeout(() => { isProgrammaticScroll = false; }, smooth ? 600 : 50);
	}

	/** Avança para o próximo card real */
	function goNext() {
		if (!carouselEl) return;
		const nextIndex = (currentIndex + 1) % total;
		goToReal(nextIndex);
	}

	// ── Drag via Pointer Events ──
	function onPointerDown(e: PointerEvent) {
		if (!carouselEl || e.button !== 0) return;
		isDragging = true;
		startX = e.clientX;
		scrollLeftStart = carouselEl.scrollLeft;
		lastMoveX = e.clientX;
		lastMoveTime = performance.now();
		carouselEl.setPointerCapture(e.pointerId);
		carouselEl.style.cursor = 'grabbing';
		carouselEl.style.userSelect = 'none';
		// Aplica will-change somente durante interação
		carouselEl.querySelectorAll('.testimonial__card').forEach(card => {
			(card as HTMLElement).style.willChange = 'transform';
		});
		stopAutoPlay();
		// Cancela momentum anterior se existir
		if (momentumFrame) { cancelAnimationFrame(momentumFrame); momentumFrame = undefined; }
	}

	function onPointerMove(e: PointerEvent) {
		if (!isDragging || !carouselEl) return;
		e.preventDefault();
		const dx = startX - e.clientX;
		carouselEl.scrollLeft = scrollLeftStart + dx;
		// Track velocity
		lastMoveX = e.clientX;
		lastMoveTime = performance.now();
	}

	/** Finaliza drag e faz snap para o card mais próximo, com momentum */
	function finishDrag() {
		if (!isDragging || !carouselEl) return;
		isDragging = false;
		carouselEl.style.cursor = '';
		carouselEl.style.userSelect = '';
		// Remove will-change após interação
		carouselEl.querySelectorAll('.testimonial__card').forEach(card => {
			(card as HTMLElement).style.willChange = '';
		});

		// Calcula velocidade do swipe (px/ms)
		const dt = performance.now() - lastMoveTime;
		const velocity = dt > 0 && dt < 300 ? (startX - lastMoveX) / dt : 0;
		const step = cardStep();

		// Aplica momentum: se velocidade alta, avança/recua cards extras
		let targetRawIdx: number;
		const absVelocity = Math.abs(velocity);
		if (absVelocity > 0.3) {
			// Swipe rápido: conta quantos cards pular baseado na velocidade
			const extraCards = Math.min(Math.floor(absVelocity * 8), total - 1);
			const direction = velocity > 0 ? 1 : -1;
			const currentRaw = carouselEl.scrollLeft / step;
			targetRawIdx = Math.round(currentRaw) + direction * extraCards;
		} else {
			targetRawIdx = Math.round(carouselEl.scrollLeft / step);
		}

		// Mapeia para índice real (0..total-1)
		if (targetRawIdx <= 0) {
			currentIndex = total - 1;
		} else if (targetRawIdx > total) {
			currentIndex = 0;
		} else {
			currentIndex = targetRawIdx - 1;
		}
		// Snap suave para a posição correta
		goToReal(currentIndex);
		// Reinicia auto-play após drag
		if (!isHovering && autoPlayActive) startAutoPlay();
	}

	function onPointerUp(_e: PointerEvent) {
		finishDrag();
	}

	/** Pointer sai do carrossel durante drag — finaliza o drag */
	function onPointerLeave(_e: PointerEvent) {
		if (isDragging) finishDrag();
	}

	/** Browser cancelou o pointer (gesto do sistema, etc.) */
	function onPointerCancel(_e: PointerEvent) {
		if (isDragging) finishDrag();
	}

	// ── Keyboard Navigation ──
	function onKeyDown(e: KeyboardEvent) {
		if (!carouselEl) return;
		if (e.key === 'ArrowLeft') {
			e.preventDefault();
			stopAutoPlay();
			const prevIndex = (currentIndex - 1 + total) % total;
			goToReal(prevIndex);
			// Reinicia auto-play com delay para permitir navegação consecutiva
			if (!isHovering) scheduleAutoPlayResume();
		} else if (e.key === 'ArrowRight') {
			e.preventDefault();
			stopAutoPlay();
			const nextIndex = (currentIndex + 1) % total;
			goToReal(nextIndex);
			if (!isHovering) scheduleAutoPlayResume();
		}
	}

	let keyboardResumeTimer: ReturnType<typeof setTimeout> | undefined;

	function scheduleAutoPlayResume() {
		if (keyboardResumeTimer) clearTimeout(keyboardResumeTimer);
		keyboardResumeTimer = setTimeout(() => {
			if (!isHovering && isInViewport && !prefersReducedMotion) {
				autoPlayActive = true;
				startAutoPlay();
			}
		}, 3000);
	}

	// ── Scroll tracking (debounced para funcionar como fallback do scrollend) ──
	function scrollHandler() {
		if (!carouselEl || isDragging) return;

		// Reseta a guarda de dupla-execução
		scrollEndHandled = false;

		// Debounce: detecta fim do scroll após 150ms de inatividade
		if (scrollEndTimer) clearTimeout(scrollEndTimer);
		scrollEndTimer = setTimeout(() => {
			if (!scrollEndHandled) handleScrollEnd();
		}, 150);
	}

	/** Lógica de wrap infinito — executa após o scroll parar */
	function handleScrollEnd() {
		if (!carouselEl || isDragging || scrollEndHandled || isProgrammaticScroll) return;
		scrollEndHandled = true;
		const step = cardStep();
		const scrollLeft = carouselEl.scrollLeft;
		const maxScroll = carouselEl.scrollWidth - carouselEl.clientWidth;

		// Wrap forward: passou do último card real → volta para o primeiro real
		if (scrollLeft >= maxScroll - step * 0.5) {
			isProgrammaticScroll = true;
			carouselEl.scrollTo({ left: step, behavior: 'instant' });
			currentIndex = 0;
			setTimeout(() => { isProgrammaticScroll = false; }, 50);
		}
		// Wrap backward: voltou para o clone do último → pula para o último real
		else if (scrollLeft <= step * 0.5) {
			isProgrammaticScroll = true;
			carouselEl.scrollTo({ left: total * step, behavior: 'instant' });
			currentIndex = total - 1;
			setTimeout(() => { isProgrammaticScroll = false; }, 50);
		}
		// Posição normal: calcula índice real
		else {
			const rawIdx = Math.round(scrollLeft / step) - 1;
			currentIndex = Math.max(0, Math.min(rawIdx, total - 1));
		}
	}

	/** scrollend nativo (Chrome 114+) — fallback para browsers modernos */
	function onNativeScrollEnd() {
		if (!carouselEl || isDragging) return;
		handleScrollEnd();
	}

	// ── Auto-play ──
	let animationFrameId: number | undefined;

	function startAutoPlay() {
		if (prefersReducedMotion) return;
		stopAutoPlay();
		let lastTime = performance.now();
		const interval = 4000;

		function tick(now: number) {
			if (!autoPlayActive || prefersReducedMotion) {
				animationFrameId = undefined;
				return;
			}
			if (now - lastTime >= interval) {
				lastTime = now;
				goNext();
			}
			animationFrameId = requestAnimationFrame(tick);
		}

		animationFrameId = requestAnimationFrame(tick);
	}

	function stopAutoPlay() {
		if (animationFrameId) {
			cancelAnimationFrame(animationFrameId);
			animationFrameId = undefined;
		}
	}

	function onSectionEnter() {
		isHovering = true;
		stopAutoPlay();
	}

	function onSectionLeave() {
		isHovering = false;
		if (isInViewport && !prefersReducedMotion) {
			startAutoPlay();
		}
	}

	// ── Lifecycle ──
	// Inicializa posição no primeiro card real (pula o clone inicial)
	$effect(() => {
		if (!carouselEl) return;
		// Pequeno delay para garantir que o layout foi calculado
		const raf = requestAnimationFrame(() => {
			if (!carouselEl) return;
			const step = cardStep();
			carouselEl.scrollTo({ left: step, behavior: 'instant' });
			currentIndex = 0;
		});
		return () => cancelAnimationFrame(raf);
	});

	$effect(() => {
		if (isInViewport && !isHovering && !prefersReducedMotion) {
			autoPlayActive = true;
			startAutoPlay();
		} else {
			autoPlayActive = false;
			stopAutoPlay();
		}
	});

	$effect(() => {
		return () => {
			stopAutoPlay();
			if (scrollEndTimer) clearTimeout(scrollEndTimer);
			if (keyboardResumeTimer) clearTimeout(keyboardResumeTimer);
			if (momentumFrame) cancelAnimationFrame(momentumFrame);
		};
	});

	// ── Anúncio para screen readers (aria-live) ──
	let liveRegionText = $state('');
	$effect(() => {
		const t = testimonials[currentIndex];
		if (t) {
			liveRegionText = `Depoimento ${currentIndex + 1} de ${total}: ${t.name}, ${t.role}`;
		}
	});
</script>

<section
	class="testimonials"
	aria-label="Depoimentos de parceiros"
	aria-roledescription="carrossel"
	bind:this={sectionEl}
	onmouseenter={onSectionEnter}
	onmouseleave={onSectionLeave}
>
	<div class="testimonials__inner">
		<div class="testimonials__header">
			<h2 class="testimonials__title">O que nossos parceiros dizem</h2>
			<p class="testimonials__subtitle">Histórias de transformação digital impulsionadas por IA.</p>
		</div>
		<div class="testimonials__carousel-wrapper">
			<!-- Fade gradients nas bordas para indicar scroll horizontal -->
			<div class="testimonials__fade testimonials__fade--left" aria-hidden="true"></div>
			<!-- svelte-ignore a11y_no_noninteractive_tabindex -->
			<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
			<div
				class="testimonials__carousel"
				class:testimonials__carousel--reduced={prefersReducedMotion}
				role="group"
				aria-label="Depoimentos"
				tabindex="0"
				bind:this={carouselEl}
				onpointerdown={onPointerDown}
				onpointermove={onPointerMove}
				onpointerup={onPointerUp}
				onpointerleave={onPointerLeave}
				onpointercancel={onPointerCancel}
				onscroll={scrollHandler}
				onscrollend={onNativeScrollEnd}
				onkeydown={onKeyDown}
			>
			<!-- Clone do último card (início, aria-hidden) -->
			<div class="testimonial__card glass-panel" aria-hidden="true">
				<div class="testimonial__stars">
					{#each Array(testimonials[total - 1].stars) as _}
						<span class="material-symbols-outlined testimonial__star">star</span>
					{/each}
				</div>
				<p class="testimonial__text">{testimonials[total - 1].text}</p>
				<div class="testimonial__author">
					<div class="testimonial__avatar">{testimonials[total - 1].initials}</div>
					<div>
						<div class="testimonial__name">{testimonials[total - 1].name}</div>
						<div class="testimonial__role">{testimonials[total - 1].role}</div>
					</div>
				</div>
			</div>

			<!-- Cards reais -->
			{#each testimonials as t, i}
				<div
					class="testimonial__card glass-panel"
					role="group"
					aria-roledescription="slide"
					aria-label={`Depoimento ${i + 1} de ${total}`}
				>
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

			<!-- Clone do primeiro card (fim, aria-hidden) -->
			<div class="testimonial__card glass-panel" aria-hidden="true">
				<div class="testimonial__stars">
					{#each Array(testimonials[0].stars) as _}
						<span class="material-symbols-outlined testimonial__star">star</span>
					{/each}
				</div>
				<p class="testimonial__text">{testimonials[0].text}</p>
				<div class="testimonial__author">
					<div class="testimonial__avatar">{testimonials[0].initials}</div>
					<div>
						<div class="testimonial__name">{testimonials[0].name}</div>
						<div class="testimonial__role">{testimonials[0].role}</div>
					</div>
				</div>
			</div>
			</div>
			<!-- Fade direito -->
			<div class="testimonials__fade testimonials__fade--right" aria-hidden="true"></div>
		</div>
		<!-- Região aria-live para screen readers anunciarem mudança de slide -->
		<div class="testimonials__sr-only" aria-live="polite" aria-atomic="true">
			{liveRegionText}
		</div>
		<div class="testimonials__dots">
			{#each testimonials as _, i}
				<button
					class="testimonials__dot"
					class:testimonials__dot--active={i === currentIndex}
					class:testimonials__dot--pulsing={i === currentIndex && !isHovering && autoPlayActive}
					onclick={() => goToReal(i)}
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

	/* ── Carousel Wrapper (para posicionar fade overlays) ── */
	.testimonials__carousel-wrapper {
		position: relative;
	}

	/* ── Fade gradients nas bordas (indicam scroll horizontal) ── */
	.testimonials__fade {
		position: absolute;
		top: 0;
		bottom: 32px; /* acompanha o padding-bottom do carousel */
		width: 48px;
		z-index: 2;
		pointer-events: none;
		transition: opacity 0.3s ease;
	}

	.testimonials__fade--left {
		left: 0;
		background: linear-gradient(to right, var(--color-background) 0%, transparent 100%);
	}

	.testimonials__fade--right {
		right: 0;
		background: linear-gradient(to left, var(--color-background) 0%, transparent 100%);
	}

	/* ── Carousel Container ── */
	.testimonials__carousel {
		display: flex;
		overflow-x: auto;
		scroll-behavior: auto; /* JS controla o smooth/instant, evita conflito */
		scroll-snap-type: x mandatory;
		gap: var(--spacing-gutter);
		padding: 0 var(--spacing-margin-mobile) 32px;
		-ms-overflow-style: none;
		scrollbar-width: none;
		cursor: grab;
		touch-action: pan-x; /* permite scroll horizontal, previne vertical */
		-webkit-overflow-scrolling: touch;
		contain: layout style paint; /* isola layout recalculations */
	}

	.testimonials__carousel:active {
		cursor: grabbing;
	}

	.testimonials__carousel::-webkit-scrollbar {
		display: none;
	}

	/* ── Reduzido: sem scroll-snap, sem animação ── */
	.testimonials__carousel--reduced {
		scroll-snap-type: none;
		scroll-behavior: auto;
	}

	.testimonials__carousel--reduced .testimonial__card {
		scroll-snap-align: none;
		transition: none;
	}

	/* ── Keyboard focus ring ── */
	.testimonials__carousel:focus-visible {
		outline: 2px solid var(--color-primary);
		outline-offset: 4px;
		border-radius: var(--radius-lg);
	}

	/* ── Card ── */
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
		width: min(350px, 80vw);
		user-select: none;
		scroll-snap-align: start;
		scroll-snap-stop: always; /* evita pular múltiplos cards em scroll rápido */
		scroll-margin: 0 var(--spacing-margin-mobile); /* garante snap visível dentro do padding */
		/* will-change aplicado via JS somente durante drag */
		transition: box-shadow 0.5s ease, transform 0.5s ease;
		contain: layout style paint; /* isola repaint do card */
	}

	.testimonial__card:hover {
		box-shadow: 0 0 30px rgba(0, 224, 255, 0.15);
	}

	@media (min-width: 768px) {
		.testimonial__card {
			width: 450px;
		}
	}

	/* ── Stars ── */
	.testimonial__stars {
		display: flex;
		gap: 4px;
		color: var(--color-primary);
	}

	.testimonial__star {
		font-size: 20px;
		font-variation-settings: 'FILL' 1;
	}

	/* ── Text ── */
	.testimonial__text {
		font-family: var(--font-body);
		font-size: var(--font-size-body-md);
		color: var(--color-on-surface);
		opacity: 0.8;
		font-style: italic;
		line-height: 1.625;
		white-space: normal;
	}

	/* ── Author ── */
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

	/* ── Dots ── */
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

	/* ── Screen-reader only (aria-live region) ── */
	.testimonials__sr-only {
		position: absolute;
		width: 1px;
		height: 1px;
		padding: 0;
		margin: -1px;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border: 0;
	}

	/* ── Auto-play pulse on active dot (composited: só opacity + transform) ── */
	.testimonials__dot--active.testimonials__dot--pulsing {
		animation: dot-pulse 2s ease-in-out infinite;
	}

	@keyframes dot-pulse {
		0%, 100% {
			opacity: 0.6;
			transform: scale(1);
		}
		50% {
			opacity: 1;
			transform: scale(1.3);
		}
	}

	/* ── prefers-reduced-motion ── */
	@media (prefers-reduced-motion: reduce) {
		.testimonials__carousel {
			scroll-behavior: auto;
		}

		.testimonial__card {
			transition: none;
		}

		.testimonials__dot--active.testimonials__dot--pulsing {
			animation: none;
		}
	}
</style>
