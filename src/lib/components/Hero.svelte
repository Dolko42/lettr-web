<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { GithubLogo, BookOpen } from 'phosphor-svelte';
	import Button from './Button.svelte';
	import CodeSnippet from './CodeSnippet.svelte';

	let section: HTMLElement | undefined = $state();

	onMount(() => {
		if (!section) return;

		const tl = gsap.timeline({ defaults: { ease: 'power3.out' } });

		tl.from(section.querySelectorAll('[data-animate]'), {
			y: 30,
			opacity: 0,
			duration: 0.7,
			stagger: 0.12
		});
	});
</script>

<section bind:this={section} class="px-4 pt-28 pb-20 border-b border-border/30" id="hero">
	<div class="mx-auto max-w-[550px]">
		<div class="grid gap-12 lg:grid-cols-1">
			<div class="flex flex-col gap-6">
				<h1 data-animate class="text-surface">
					Transactional emails that<br />
					<span class="text-primary">feel like Laravel</span>
				</h1>

				<p data-animate class="max-w-md text-body text-muted">
					Lettr is the developer-first email API. Send transactional emails at scale with a simple
					REST API, SDKs for every language, and real-time delivery analytics.
				</p>

				<div data-animate class="flex flex-wrap items-center mt-4 gap-2">
					<Button variant="primary" href="#signup">Register</Button>
					<Button variant="secondary" href="#pricing">See the editor </Button>
				</div>
				<p data-animate class="max-w-md mt-[-12px] text-sm text-gray-400">
					3,000 emails/month free. No credit card required.
				</p>

				<div data-animate class="mt-4">
					<CodeSnippet />
				</div>

				<div data-animate class="flex items-center gap-4">
					<a
						href="#docs"
						class="inline-flex items-center gap-1.5 text-sm text-muted transition-colors hover:text-surface"
					>
						<BookOpen size={16}  />
						Read the docs
					</a>
					<a
						href="https://github.com"
						class="inline-flex items-center gap-1.5 text-sm text-muted transition-colors hover:text-surface"
						target="_blank"
						rel="noopener noreferrer"
					>
						<GithubLogo size={16} />
						view on GitHub
					</a>
				</div>
			</div>
		</div>
	</div>
</section>
