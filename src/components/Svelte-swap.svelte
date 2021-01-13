<script lang="ts">
	import { quintInOut } from 'svelte/easing';

    const isBooleanPropertySet = (val: '' | undefined | null | boolean) => (val ?? true) || false;

    function clipCircle(node, { duration = 500 }) {
        const slot = node?.querySelector('slot') as HTMLSlotElement;
        const wrapped = slot?.assignedNodes()[0] as HTMLElement;

		const parentRect = wrapped.parentElement.getClientRects()[0];
		const parentCenterY = parentRect.y + parentRect.height/2;
		const parentCenterX = parentRect.x + parentRect.width/2;

		const thisRect = wrapped.getClientRects()[0];
		const thisCenterY = thisRect.y + thisRect.height/2;
		const thisCenterX = thisRect.x + thisRect.width/2;

		const left = (parentCenterX - thisCenterX)*2;
		const top = (parentCenterY - thisCenterY)*2;
        
		return {
			duration,
			css: t => {
				const eased = quintInOut(t);
                wrapped.style.margin = `${top - eased*top}px 0 0 ${top - eased*top}px`;
                wrapped.style.clipPath = `circle(${eased * 100}%)`;
				return `background-color: rgb(${255 * eased},${255 * eased},${0 * eased})`
			}
		};
	}

    export let active: boolean;

    let wrapper2: HTMLElement;
    
    $: shown = isBooleanPropertySet(active);
</script>

<style lang="scss">

</style>

<svelte:options tag="svelte-swap"></svelte:options>

<div>
    {#if shown}
    <div>
        <slot></slot>
    </div>
    {:else}
    <div bind:this={wrapper2} transition:clipCircle>
        <slot name="active"></slot>
    </div>
    {/if}
</div>