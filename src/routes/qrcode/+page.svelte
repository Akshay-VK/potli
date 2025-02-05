<script lang="ts">
    import { Button } from "$lib/components/ui/button/index.js";
    import { Input } from "$lib/components/ui/input/index.js";
	import Base from "../Base.svelte";
    import qrcode from "qrcode-generator";
    import * as Alert from "$lib/components/ui/alert/index.js";

    var text = $state("")

    var q = qrcode(0,"M");

    var url = $state("");

    function update_qr(){
        console.log(text);
        q=qrcode(0,"M");
        q.addData(text);
        q.make();
        url= q.createDataURL();
    }
</script>

{#snippet content()}
<h1 class="scroll-m-20 text-3xl font-extrabold tracking-tight lg:text-4xl pb-8">
    Text to QR code generator
</h1>
<div class="grid gap-2 sm:gap-2 grid-rows-2 grid-flow-row w-4/5">
    <div>
        <form class="flex flex-col sm:flex-row w-full items-center space-x-0 space-y-3 sm:space-y-0 sm:space-x-2">
            <Input class="sm:flex-auto" bind:value={text} placeholder="Type text to convert to QR code..." />
            <Button class="sm:flex-none w-full sm:w-fit" on:click={()=>update_qr()}>Generate</Button>
        </form>
    </div>
    <div>
        {#if url==""}
            <Alert.Root>
            <Alert.Title>No image generated yet.</Alert.Title>
            <Alert.Description>Either the <b>Generate</b> button wasn't clicked or the input is wayy too long.</Alert.Description>
            </Alert.Root>
        {:else}
            <img class="border" alt="Some problem occured!" src={url}/>
            <br>
            <p class="text-muted-foreground text-sm"><i>Generated QR code.</i></p>
        {/if}
    </div>
</div>


{/snippet}

<Base {content}/>