<script context="module" lang="ts">
	export type ButtonProps = ButtonPrimitive.Props & {
		variant?: Variant;
		size?: Size;
	};
</script>

<script lang="ts">
	import { cn } from '$lib/utils';
	import { Button as ButtonPrimitive } from 'bits-ui';
	import { buttonVariants, type Size, type Variant } from '.';

	type $$Props = ButtonProps;
	type $$Events = ButtonPrimitive.Events;

	let className: $$Props['class'] = undefined;
	export let variant: $$Props['variant'] = 'default';
	export let size: $$Props['size'] = 'default';
	export let builders: $$Props['builders'] = [];
	export { className as class };
</script>

<ButtonPrimitive.Root
	{builders}
	class={cn(buttonVariants({ variant, size, className }))}
	{...$$restProps}
	on:click
	on:keydown
>
	<slot />
</ButtonPrimitive.Root>
