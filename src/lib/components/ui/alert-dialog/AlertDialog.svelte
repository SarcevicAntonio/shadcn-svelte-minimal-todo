<script lang="ts">
	import { createDialog, melt } from '@melt-ui/svelte';
	import { X } from 'lucide-svelte';
	import { createEventDispatcher, tick } from 'svelte';
	import { fade, fly } from 'svelte/transition';
	import type { Variant } from '../button';
	import Button from '../button/button.svelte';

	const dispatch = createEventDispatcher();

	const {
		elements: { trigger, overlay, content, title, description, close, portalled },
		states: { open }
	} = createDialog({ role: 'alertdialog' });

	export let excludeTrigger = false;
	export let triggerVariant: Variant = 'default';
	export let triggerLabel = 'Delete';
	export let confirmVariant: Variant = 'destructive';
</script>

{#if !excludeTrigger}
	<Button builders={[$trigger]} variant={triggerVariant}>
		<slot name="trigger-label">{triggerLabel}</slot>
	</Button>
{/if}

<div use:melt={$portalled}>
	{#if $open}
		<div
			use:melt={$overlay}
			class="fixed inset-0 z-50 bg-black/50"
			transition:fade={{ duration: 150 }}
		/>
		<div
			class="fixed left-[50%] top-[50%] z-50 max-h-[85vh] w-[90vw]
             max-w-[450px] translate-x-[-50%] translate-y-[-50%] rounded-md bg-white
             p-6 shadow-lg"
			transition:fly={{
				duration: 150,
				y: 8,
				opacity: 0
			}}
			use:melt={$content}
		>
			<h2 use:melt={$title} class="m-0 text-lg font-medium text-black">
				<slot name="title">Are you sure you want to delete this?</slot>
			</h2>
			<p use:melt={$description} class="mb-5 mt-2 leading-normal text-zinc-600">
				<slot name="description">
					This action cannot be undone. This will permanently delete the item and remove it from our
					servers.
				</slot>
			</p>
			<div class="mt-6 flex justify-end gap-4">
				<Button builders={[$close]} variant="outline">Cancel</Button>
				<Button
					builders={[$close]}
					on:click={async () => {
						$open = false;
						dispatch('confirm');
					}}
					variant={confirmVariant}>Confirm</Button
				>
			</div>

			<Button
				builders={[$close]}
				aria-label="Close"
				variant="ghost"
				size="icon"
				class="absolute right-[10px] top-[10px]  h-6 w-6 text-xs"
			>
				<X class="square-2" />
			</Button>
		</div>
	{/if}
</div>
