<script lang="ts">
	import { tick } from 'svelte';
	import testimonials from '$lib/data/testimonials.json';

	// ── Types ──
	interface Testimonial {
		stars: number;
		text: string;
		initials: string;
		name: string;
		role: string;
	}

	const typed = testimonials as Testimonial[];
	const total = typed.length;

	// ── Reactive State ──
	let carouselEl = $state<HTMLElement>();
	let sectionEl = $state<HTMLElement>();
	let scrollLeft = $state(0);
	let cardsPerView = $state<number>(1);
	let step = $state<number>(0);
	let isHovering = $state(false);
	let prefersReducedMotion = $state(false);
	let isInViewport = $state(false);
	let autoPlayActive = $state(true);
	let isDragging = $state(false);
	let noSnap = $state(false);

	// ── Non-reactive state ──
	let startX = 0;
	let scrollLeftStart = 0;
	let lastMoveX = 0;
	let lastMoveTime = 0;
	let momentumFrame: number | undefined;
	let isJumping = false;
	let animationFrameId: number | undefined;
	let keyboardResumeTimer: ReturnType<typeof setTimeout> | undefined;
	let jumpFallbackTimer: ReturnType<typeof setTimeout> | undefined;

	// ── Derived: displayBlock ──
	// Repete itens até largura ≥ (cardsPerView + 2) × cardWidth
	// Cap: displayBlock.length ≤ testimonials.length × 2
	let displayBlock = $derived.by(() => {
		if (!typed.length) return [];
		const needed = Math.max(1, Math.ceil((cardsPerView + 2) / total));
		const copies = Math.min(needed, 2);
		return Array.from({ length: copies }, () => typed).flat();
	});

	let blockLen = $derived(displayBlock.length);

	// ── Derived: tripleBlock ──
	let tripleBlock = $derived([...displayBlock, ...displayBlock, ...displayBlock]);

	// ── Derived: realIndex ──
	let realIndex = $derived.by(() => {
		if (!blockLen || !step) return 0;
		return (Math.round(scrollLeft / step) % blockLen) % total;
	});

	// ── Derived: live region text ──
	let liveRegionText = $derived.by(() => {
		const t = typed[realIndex];
		if (!t) return '';
		return `Depoimento ${realIndex + 1} de ${total}: ${t.name}, ${t.role}`;
	});

	// ── prefers-reduced-motion ──
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

	// ── Single ResizeObserver no container do carrossel ──
	$effect(() => {
		if (!carouselEl) return;

		function updateMetrics() {
			if (!carouselEl) return;
			const containerWidth = carouselEl.clientWidth;
			const firstCard = carouselEl.querySelector('.testimonial__card') as HTMLElement | null;
			if (!firstCard) return;

			const cardWidth = firstCard.offsetWidth;
			const gap = 24;
			const newStep = cardWidth + gap;
			const availableWidth = containerWidth - 32; // padding: 16px each side
			const newCardsPerView = Math.max(1, Math.floor(availableWidth / newStep));

			if (newStep !== step || newCardsPerView !== cardsPerView) {
				const savedIndex = realIndex;
				step = newStep;
				cardsPerView = newCardsPerView;

				// Restore position after resize
				requestAnimationFrame(() => {
					if (!carouselEl) return;
					const target = blockLen * step + savedIndex * step;
					carouselEl.scrollTo({ left: target, behavior: 'instant' });
					scrollLeft = carouselEl.scrollLeft;
				});
			}
		}

		const observer = new ResizeObserver(updateMetrics);
		observer.observe(carouselEl);
		// Initial measurement
		updateMetrics();
		return () => observer.disconnect();
	});

	// ── Initial scroll position (middle block start) ──
	let initDone = false;

	$effect(() => {
		if (!carouselEl || !step || !blockLen || initDone) return;

		tick().then(() => {
			if (!carouselEl) return;
			const target = blockLen * step;
			carouselEl.scrollTo({ left: target, behavior: 'instant' });
			scrollLeft = carouselEl.scrollLeft;
			initDone = true;
		});
	});

	// ── Navegação ──
	function goToReal(index: number, smooth = true) {
		if (!carouselEl || !blockLen) return;
		// Não rouba foco de elementos interativos
		const activeTag = document.activeElement?.tagName;
		if (activeTag === 'INPUT' || activeTag === 'TEXTAREA' || activeTag === 'SELECT') return;

		const target = (blockLen + index) * step;
		isJumping = true;
		carouselEl.scrollTo({ left: target, behavior: smooth && !prefersReducedMotion ? 'smooth' : 'instant' });
		scrollLeft = target;

		const releaseJump = () => {
			isJumping = false;
		};
		carouselEl.addEventListener('scrollend', releaseJump, { once: true });
		// Fallback timeout for browsers without scrollend support
		if (jumpFallbackTimer) clearTimeout(jumpFallbackTimer);
		jumpFallbackTimer = setTimeout(releaseJump, smooth ? 500 : 80);
	}

	/** Avança para o próximo depoimento real */
	function goNext() {
		const next = (realIndex + 1) % total;
		goToReal(next, true);
	}

	// ── Scroll Handler com Boundary Detection ──
	function scrollHandler() {
		if (!carouselEl || isDragging || isJumping) return;

		const currentScroll = carouselEl.scrollLeft;
		scrollLeft = currentScroll;

		const blockWidth = blockLen * step;
		if (!blockWidth) return;

		// Block 1 → jump to block 2
		if (currentScroll < blockWidth) {
			isJumping = true;
			noSnap = true;
			const newScroll = currentScroll + blockWidth;
			carouselEl.scrollLeft = newScroll;
			scrollLeft = newScroll;

			const releaseJump = () => {
				isJumping = false;
				noSnap = false;
			};
			requestAnimationFrame(releaseJump);
			if (jumpFallbackTimer) clearTimeout(jumpFallbackTimer);
			jumpFallbackTimer = setTimeout(releaseJump, 80);
		}
		// Block 3 → jump to block 2
		else if (currentScroll >= 2 * blockWidth) {
			isJumping = true;
			noSnap = true;
			const newScroll = currentScroll - blockWidth;
			carouselEl.scrollLeft = newScroll;
			scrollLeft = newScroll;

			const releaseJump = () => {
				isJumping = false;
				noSnap = false;
			};
			requestAnimationFrame(releaseJump);
			if (jumpFallbackTimer) clearTimeout(jumpFallbackTimer);
			jumpFallbackTimer = setTimeout(releaseJump, 80);
		}
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
		// Apply will-change during interaction
		carouselEl.querySelectorAll('.testimonial__card').forEach(card => {
			(card as HTMLElement).style.willChange = 'transform';
		});
		if (momentumFrame) { cancelAnimationFrame(momentumFrame); momentumFrame = undefined; }
		stopAutoPlay();
	}

	function onPointerMove(e: PointerEvent) {
		if (!isDragging || !carouselEl) return;
		e.preventDefault();
		const dx = startX - e.clientX;
		carouselEl.scrollLeft = scrollLeftStart + dx;
		scrollLeft = carouselEl.scrollLeft;
		lastMoveX = e.clientX;
		lastMoveTime = performance.now();
	}

	function finishDrag() {
		if (!isDragging || !carouselEl) return;
		isDragging = false;
		carouselEl.style.cursor = '';
		carouselEl.style.userSelect = '';
		// Remove will-change
		carouselEl.querySelectorAll('.testimonial__card').forEach(card => {
			(card as HTMLElement).style.willChange = '';
		});

		// Calculate swipe velocity
		const dt = performance.now() - lastMoveTime;
		const velocity = dt > 0 && dt < 300 ? (startX - lastMoveX) / dt : 0;
		const absVelocity = Math.abs(velocity);

		const currentScroll = carouselEl.scrollLeft;
		let targetIdx = Math.round(currentScroll / step);

		// Momentum: skip extra cards on fast swipe
		if (absVelocity > 0.3) {
			const extraCards = Math.min(Math.floor(absVelocity * 8), total - 1);
			const direction = velocity > 0 ? 1 : -1;
			targetIdx += direction * extraCards;
		}

		// Normalize to middle block
		const blockWidth = blockLen * step;
		if (targetIdx < blockLen) {
			targetIdx += blockLen;
		} else if (targetIdx >= 2 * blockLen) {
			targetIdx -= blockLen;
		}

		// Smooth scroll to target
		isJumping = true;
		noSnap = true;
		carouselEl.scrollTo({ left: targetIdx * step, behavior: 'smooth' });
		scrollLeft = targetIdx * step;

		const releaseJump = () => {
			isJumping = false;
			noSnap = false;
			carouselEl?.removeEventListener('scrollend', releaseJump);
		};
		carouselEl.addEventListener('scrollend', releaseJump, { once: true });
		if (jumpFallbackTimer) clearTimeout(jumpFallbackTimer);
		jumpFallbackTimer = setTimeout(releaseJump, 500);

		// Resume auto-play after drag
		if (!isHovering && autoPlayActive) startAutoPlay();
	}

	function onPointerUp(_e: PointerEvent) {
		finishDrag();
	}

	function onPointerLeave(_e: PointerEvent) {
		if (isDragging) finishDrag();
	}

	function onPointerCancel(_e: PointerEvent) {
		if (isDragging) finishDrag();
	}

	// ── Keyboard Navigation ──
	function onKeyDown(e: KeyboardEvent) {
		if (!carouselEl) return;
		if (e.key === 'ArrowLeft') {
			e.preventDefault();
			stopAutoPlay();
			goToReal((realIndex - 1 + total) % total);
			if (!isHovering) scheduleAutoPlayResume();
		} else if (e.key === 'ArrowRight') {
			e.preventDefault();
			stopAutoPlay();
			goToReal((realIndex + 1) % total);
			if (!isHovering) scheduleAutoPlayResume();
		}
	}

	function scheduleAutoPlayResume() {
		if (keyboardResumeTimer) clearTimeout(keyboardResumeTimer);
		keyboardResumeTimer = setTimeout(() => {
			if (!isHovering && isInViewport && !prefersReducedMotion) {
				autoPlayActive = true;
				startAutoPlay();
			}
		}, 3000);
	}

	// ── Auto-play (requestAnimationFrame) ──
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

	// ── Autoplay lifecycle ──
	$effect(() => {
		if (isInViewport && !isHovering && !prefersReducedMotion) {
			autoPlayActive = true;
			startAutoPlay();
		} else {
			autoPlayActive = false;
			stopAutoPlay();
		}
	});

	// ── Cleanup ──
	$effect(() => {
		return () => {
			stopAutoPlay();
			if (keyboardResumeTimer) clearTimeout(keyboardResumeTimer);
			if (momentumFrame) cancelAnimationFrame(momentumFrame);
			if (jumpFallbackTimer) clearTimeout(jumpFallbackTimer);
		};
	});
</script>

{#if !testimonials || testimonials.length === 0}
	<!-- Empty guard clause: renderiza fallback silencioso -->
{:else}
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
				<!-- Fade left gradient -->
				<div class="testimonials__fade testimonials__fade--left" aria-hidden="true"></div>

				<!-- svelte-ignore a11y_no_noninteractive_tabindex -->
				<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
				<div
					class="testimonials__carousel"
					class:testimonials__carousel--reduced={prefersReducedMotion}
					class:testimonials__carousel--no-snap={noSnap}
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
					onkeydown={onKeyDown}
				>
					{#each tripleBlock as t, idx}
						{@const idxInBlock = idx % blockLen}
						{@const isBlock1 = idx < blockLen}
						{@const isBlock3 = idx >= 2 * blockLen}
						{@const isMiddle = !isBlock1 && !isBlock3}
						<div
							class="testimonial__card glass-panel"
							aria-hidden={isBlock1 || isBlock3}
							role={isMiddle ? 'group' : null}
							aria-roledescription={isMiddle ? 'slide' : null}
							aria-label={isMiddle ? `Depoimento ${(idxInBlock % total) + 1} de ${total}` : null}
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
				</div>

				<!-- Fade right gradient -->
				<div class="testimonials__fade testimonials__fade--right" aria-hidden="true"></div>
			</div>

			<!-- Região aria-live para screen readers -->
			<div class="testimonials__sr-only" aria-live="polite" aria-atomic="true">{liveRegionText}</div>

			<!-- Dots de navegação -->
			<div class="testimonials__dots">
				{#each typed as _, i}
					<button
						class="testimonials__dot"
						class:testimonials__dot--active={i === realIndex}
						class:testimonials__dot--pulsing={i === realIndex && !isHovering && autoPlayActive}
						onclick={() => goToReal(i)}
						aria-label={`Depoimento ${i + 1}`}
					></button>
				{/each}
			</div>
		</div>
	</section>
{/if}

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

	/* ── No snap (during boundary jumps) ── */
	.testimonials__carousel--no-snap {
		scroll-snap-type: none;
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
		box-shadow: var(--shadow-glow-primary);
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
		font-size: var(--font-size-body-md);
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
