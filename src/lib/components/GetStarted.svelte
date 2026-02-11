<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';
	import { BookOpenIcon, CaretDownIcon } from 'phosphor-svelte';
	import CodeSnippet from './CodeSnippet.svelte';
	import TerminalCommand from './TerminalCommand.svelte';
	import type { CodeTab } from '$lib/utils/shiki';

	gsap.registerPlugin(ScrollTrigger);

	let section: HTMLElement | undefined = $state();
	let dropdownEl: HTMLElement | undefined = $state();
	let dropdownOpen: boolean = $state(false);

	type Framework = 'laravel' | 'nodejs';
	let activeFramework: Framework = $state('laravel');

	const frameworks: { value: Framework; label: string }[] = [
		{ value: 'laravel', label: 'Laravel' },
		{ value: 'nodejs', label: 'Node.js' }
	];

	let activeLabel = $derived(frameworks.find((f) => f.value === activeFramework)!.label);

	interface Step {
		number: number;
		title: string;
		type: 'terminal' | 'code';
		laravel: string | string[];
		nodejs: string | string[];
		laravelLang?: string;
		nodejsLang?: string;
		laravelFilename?: string;
		nodejsFilename?: string;
	}

	const steps: Step[] = [
		{
			number: 1,
			title: 'Install the SDK',
			type: 'terminal',
			laravel: ['composer require lettr/lettr-laravel'],
			nodejs: ['npm install @lettr/node']
		},
		{
			number: 2,
			title: 'Initialize',
			type: 'terminal',
			laravel: ['php artisan lettr:init'],
			nodejs: `import { Lettr } from '@lettr/node';

const lettr = new Lettr(process.env.LETTR_API_KEY);`
		},
		{
			number: 3,
			title: 'Build your email',
			type: 'code',
			laravel: `$email = $lettr->emails()->create()
    ->from('hello@yourapp.com', 'Your App')
    ->to([$user->email])
    ->subject('Welcome!')
    ->html($html);`,
			laravelLang: 'php',
			laravelFilename: 'app/Mail/WelcomeMail.php',
			nodejs: `const email = lettr.emails.create({
  from: 'hello@yourapp.com',
  to: user.email,
  subject: 'Welcome!',
  html: html,
});`,
			nodejsLang: 'javascript',
			nodejsFilename: 'src/mail.js'
		},
		{
			number: 4,
			title: 'Send it',
			type: 'code',
			laravel: `$response = $lettr->emails()->send($email);

echo $response->requestId;`,
			laravelLang: 'php',
			laravelFilename: 'routes/web.php',
			nodejs: `const response = await lettr.emails.send(email);

console.log(response.requestId);`,
			nodejsLang: 'javascript',
			nodejsFilename: 'src/send.js'
		}
	];

	function getContent(step: Step): string | string[] {
		return activeFramework === 'laravel' ? step.laravel : step.nodejs;
	}

	function getLang(step: Step): string {
		return activeFramework === 'laravel' ? (step.laravelLang ?? 'php') : (step.nodejsLang ?? 'javascript');
	}

	function getFilename(step: Step): string | undefined {
		return activeFramework === 'laravel' ? step.laravelFilename : step.nodejsFilename;
	}

	function getCodeTab(step: Step): CodeTab[] {
		const content = getContent(step);
		const code = Array.isArray(content) ? content.join('\n') : content;
		return [{ label: activeLabel, lang: getLang(step), code }];
	}

	function selectFramework(fw: Framework) {
		activeFramework = fw;
		dropdownOpen = false;
	}

	function handleClickOutside(e: MouseEvent) {
		if (dropdownEl && !dropdownEl.contains(e.target as Node)) {
			dropdownOpen = false;
		}
	}

	onMount(() => {
		if (!section) return;

		document.addEventListener('click', handleClickOutside);

		gsap.from(section.querySelectorAll('[data-step]'), {
			scrollTrigger: {
				trigger: section,
				start: 'top 80%',
				toggleActions: 'play none none none'
			},
			y: 30,
			opacity: 0,
			duration: 0.6,
			stagger: 0.12,
			ease: 'power3.out'
		});

		return () => document.removeEventListener('click', handleClickOutside);
	});
</script>

<section bind:this={section} class="px-4 py-16 border-b border-border/30">
	<div class="mx-auto max-w-[550px]">
		<div class="mb-12 flex items-center justify-between gap-4" data-step>
			<div>
				<h2 class="mb-3 text-surface">Get started <span class="text-primary">in minutes</span></h2>
				<p class="text-body text-muted">
					Four steps to sending your first email. <br /> No complicated setup.
				</p>
			</div>

			<div class="relative shrink-0" bind:this={dropdownEl}>
				<button
					class="flex items-center gap-1.5 border border-border/30 bg-white px-3 py-1.5 text-sm font-medium text-surface transition-colors hover:bg-background"
					onclick={() => (dropdownOpen = !dropdownOpen)}
				>
					{activeLabel}
					<CaretDownIcon size={10} />
				</button>
				{#if dropdownOpen}
					<div class="absolute right-0 top-full z-50 mt-1 min-w-[140px] border border-border/30 bg-white py-1 shadow-lg">
						{#each frameworks as fw}
							<button
								class="block w-full px-3 py-1.5 text-left text-[13px] transition-colors {activeFramework === fw.value
									? 'text-primary font-medium'
									: 'text-muted hover:text-surface'}"
								onclick={() => selectFramework(fw.value)}
							>
								{fw.label}
							</button>
						{/each}
					</div>
				{/if}
			</div>
		</div>

		<div class="flex flex-col gap-10">
			{#each steps as step}
				<div data-step>
					<h3 class="mb-3 text-surface">
						<span class="text-primary">{step.number}.</span> {step.title}
					</h3>

					{#if step.type === 'terminal'}
						{@const content = getContent(step)}
						{#if Array.isArray(content)}
							<TerminalCommand commands={content} />
						{:else}
							<CodeSnippet tabs={getCodeTab(step)} primaryTabIndices={[0]} moreTabIndices={[]} shadow={false} filename={getFilename(step)} />
						{/if}
					{:else}
						<CodeSnippet tabs={getCodeTab(step)} primaryTabIndices={[0]} moreTabIndices={[]} shadow={false} filename={getFilename(step)} />
					{/if}
				</div>
			{/each}
		</div>

		<div class="mt-10" data-step>
			<a
				href="https://docs.lettr.com/introduction"
				class="inline-flex items-center gap-1.5 text-sm text-muted transition-colors hover:text-surface hover:underline"
				target="_blank"
				rel="noopener noreferrer"
			>
				<BookOpenIcon size={16} class="text-primary" />
				View setup guides for Python, Go, Ruby, and more
			</a>
		</div>
	</div>
</section>
