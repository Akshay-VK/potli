<script lang="ts">
    let {to,text} = $props();
    import { Button } from "$lib/components/ui/button/index.js";

    import { onMount } from 'svelte';

    let check = $state(false)

    onMount(() => {
        check=window.location.pathname==to;
    });
</script>
<div class="h-fit">
    <Button variant="link">
        {#if check}
        <a class="font-bold" href={to}>{text}</a>
        {:else}
        <a class="font-medium" href={to}>{text}</a> 
        {/if}
    </Button>
</div>