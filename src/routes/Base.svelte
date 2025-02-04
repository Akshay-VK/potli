<script lang="ts">
	import Sidebar from "./Sidebar.svelte";
	import Navbar from "./Navbar.svelte";
    import ScrollArea from "$lib/components/ui/scroll-area/scroll-area.svelte";

    let {content} = $props();
</script>

<div class="bg-slate-50 flex flex-col dark:bg-slate-950 w-dvw h-screen divide-y dark:text-slate-50 text-slate-950 overflow-hidden">
   <Navbar/> 
    <div class="flex flex-row flex-auto">
        <div class="sm:block hidden flex-none w-64 h-3/4"><Sidebar/></div>
        <div class="flex-auto leading-7 [&:not(:first-child)]:mt-6 ml-6 sm:ml-4 overflow-hidden h-screen">
            <ScrollArea class="h-4/5 py-1 pr-4">
                {@render content()}
            </ScrollArea>
        </div>
    </div>
</div>