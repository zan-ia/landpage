<footer class="footer">
	<div class="footer__inner">
		<div class="footer__brand">
			<div class="footer__logo">
				<span class="material-symbols-outlined footer__icon">memory</span>
				<span class="footer__title">ZAN.IA</span>
			</div>
			<p class="footer__copyright">&copy; 2024 ZAN.IA DEEP TECH SYSTEMS. ALL RIGHTS RESERVED.</p>
		</div>
		<nav class="footer__nav">
			<!-- svelte-ignore a11y_invalid_attribute -->
			<a class="footer__link" href="#">SOLUTIONS</a>
			<!-- svelte-ignore a11y_invalid_attribute -->
			<a class="footer__link" href="#">NEURAL NETWORKS</a>
			<!-- svelte-ignore a11y_invalid_attribute -->
			<a class="footer__link" href="#">TERMINAL</a>
			<!-- svelte-ignore a11y_invalid_attribute -->
			<a class="footer__link" href="#">CONTACT</a>
		</nav>
	</div>
	<div>
		<p class="footer__version">
			version 1.0.0
		</p>
	</div>
</footer>

<style>
	.footer {
		width: 100%;
		padding: 48px var(--spacing-margin-mobile);
		border-top: 1px solid rgba(59, 73, 76, 0.1);
	}

	@media (min-width: 768px) {
		.footer {
			padding: 48px var(--spacing-margin-desktop);
		}
	}

	.footer__inner {
		max-width: var(--spacing-container-max);
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: var(--spacing-base);
	}

	@media (min-width: 768px) {
		.footer__inner {
			flex-direction: row;
			justify-content: space-between;
			align-items: center;
		}
	}

	.footer__brand {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 16px;
	}

	@media (min-width: 768px) {
		.footer__brand {
			align-items: flex-start;
		}
	}

	.footer__logo {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.footer__icon {
		color: var(--color-primary);
		font-size: 20px;
	}

	.footer__title {
		font-family: var(--font-display);
		font-size: var(--font-size-headline-md);
		color: var(--color-on-surface);
		letter-spacing: -0.05em;
	}

	.footer__copyright {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.5;
	}

	.footer__version {
		opacity: 0.5;
		margin-top: 16px;
		text-align: center;
		font-family: var(--font-code);
		color: var(--color-on-surface);
		font-size: var(--font-size-label-sm);
	}

	.footer__nav {
		display: flex;
		gap: 32px;
	}

	.footer__link {
		font-family: var(--font-code);
		font-size: var(--font-size-label-sm);
		color: var(--color-on-surface);
		opacity: 0.7;
		text-decoration: none;
		transition: color 0.2s;
	}

	.footer__link:hover {
		color: var(--color-primary);
		text-decoration: underline;
		text-underline-offset: 4px;
	}
</style>
