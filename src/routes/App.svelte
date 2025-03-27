<script>
	import Base from "./Base.svelte";

</script>

{#snippet content()}
    <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">
        potli
    </h1>
    <p class="text-muted-foreground text-xl [&:not(:first-child)]:mt-4">
        A bundle of clean and practical mini apps.
    </p>
{/snippet}

<Base {content} title='Home'/>