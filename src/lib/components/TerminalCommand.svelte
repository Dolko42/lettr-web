<script lang="ts">
	import { CopyIcon, CheckIcon, TerminalWindowIcon } from 'phosphor-svelte';

	interface Props {
		commands: string[];
	}

	let { commands }: Props = $props();

	let copied: boolean = $state(false);

	async function copyCommands() {
		await navigator.clipboard.writeText(commands.join('\n'));
		copied = true;
		setTimeout(() => {
			copied = false;
		}, 3000);
	}
</script>

<div class="w-full border border-border/30 bg-white">
	<div class="flex items-center justify-between px-3 py-1.5">
		<div class="flex items-center gap-1.5 text-[12px] text-muted">
			<TerminalWindowIcon size={14} />
			Terminal
		</div>
		<button
			class="flex shrink-0 items-center gap-1.5 p-1.5 text-muted transition-colors hover:text-surface text-[12px]"
			onclick={copyCommands}
			aria-label="Copy command"
		>
			{#if copied}
				<CheckIcon size={14} />
				Copy code
			{:else}
				<CopyIcon size={14} />
				Copy code
			{/if}
		</button>
	</div>

	<div class="border-t border-border/30 bg-background px-4 py-3">
		{#each commands as command}
			<div class="flex items-center gap-2 font-code text-[13px] leading-[1.6]">
				<span class="select-none text-primary">$</span>
				<span class="text-surface">{command}</span>
			</div>
		{/each}
	</div>
</div>
