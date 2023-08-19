<script lang="ts">
	import AlertDialog from '$lib/components/ui/alert-dialog/AlertDialog.svelte';
	import { Button } from '$lib/components/ui/button';
	import { Checkbox } from '$lib/components/ui/checkbox';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';
	import { Pencil, Trash2 } from 'lucide-svelte';
	import { tick } from 'svelte';
	import { flip } from 'svelte/animate';
	import { fade } from 'svelte/transition';

	let todos = [
		{
			title: 'Check out shadcn-svelte',
			id: crypto.randomUUID(),
			done: false,
			edit: undefined as string | undefined,
			editInputRef: undefined as HTMLInputElement | undefined
		}
	];

	let new_todo = '';
</script>

<form
	on:submit|preventDefault={() => {
		if (!new_todo.trim().length) return;
		todos.push({
			title: new_todo,
			id: crypto.randomUUID(),
			done: false,
			edit: undefined,
			editInputRef: undefined
		});
		todos = todos;
		new_todo = '';
	}}
	class="mb-4"
>
	<Label>Create a new Todo:</Label>
	<Input bind:value={new_todo} />
</form>

<ul class="flex flex-col gap-3">
	{#each todos as todo (todo.id)}
		<li class="flex items-center space-x-2" transition:fade animate:flip>
			{#if typeof todo.edit !== 'string'}
				<Checkbox id={todo.id} bind:checked={todo.done} />
				<Label for={todo.id} class="grow {todo.done ? 'line-through opacity-60' : ''}">
					{todo.title}
				</Label>
				<Button
					on:click={async () => {
						todo.edit = todo.title;
						await tick();
						todo.editInputRef?.select();
					}}
					variant="ghost"
					size="icon"
				>
					<Pencil />
				</Button>
				<AlertDialog
					triggerProps={{ variant: 'ghost', size: 'icon', class: 'text-red-700' }}
					on:confirm={() => {
						todos = todos.filter((t) => t.id !== todo.id);
					}}
				>
					<Trash2 slot="trigger-label" />
				</AlertDialog>
			{:else}
				<Label for={todo.id}>
					<Pencil />
				</Label>
				<Input
					bind:value={todo.edit}
					class="grow"
					bind:ref={todo.editInputRef}
					on:keydown={(e) => {
						if (e.key === 'Escape') {
							todo.title = todo.edit + '';
							todo.edit = undefined;
						}
					}}
				/>
				<Button
					on:click={() => {
						todo.title = todo.edit + '';
						todo.edit = undefined;
					}}
				>
					Confirm
				</Button>
				<Button
					on:click={() => {
						todo.edit = undefined;
					}}
					variant="outline"
				>
					Cancel
				</Button>
			{/if}
		</li>
	{/each}
</ul>
