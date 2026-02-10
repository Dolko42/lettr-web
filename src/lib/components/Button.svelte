<script lang="ts">
	import { onMount, type Snippet } from 'svelte';
	import gsap from 'gsap';

	interface Props {
		variant?: 'primary' | 'secondary';
		href?: string;
		onclick?: () => void;
		children?: Snippet;
	}

	let { variant = 'primary', href, onclick, children, ...rest }: Props & Record<string, unknown> =
		$props();

	let el: HTMLElement | undefined = $state();

	onMount(() => {
		if (!el) return;
		gsap.set(el, { y: 0 });

		el.addEventListener('mouseenter', () => {
			gsap.to(el!, { y: -2, duration: 0.2, ease: 'power2.out' });
		});
		el.addEventListener('mouseleave', () => {
			gsap.to(el!, { y: 0, duration: 0.2, ease: 'power2.out' });
		});
	});

	const baseClasses =
		'inline-flex items-center justify-center min-w-[180px] px-6 py-3 font-bold transition-colors cursor-pointer';
	const variants = {
		primary: 'bg-primary text-white hover:bg-primary/90',
		secondary: 'text-primary bg-white hover:bg-primary/5'
	};
</script>

{#if href}
	<a bind:this={el} {href} class="{baseClasses} {variants[variant]}" {...rest}>
		{#if children}{@render children()}{/if}
	</a>
{:else}
	<button bind:this={el} {onclick} class="{baseClasses} {variants[variant]}" {...rest}>
		{#if children}{@render children()}{/if}
	</button>
{/if}
