<script>
    import { Button } from "$lib/components/ui/button/index";
    import PanelLeft from "lucide-svelte/icons/panel-left";
    import * as Sheet from "$lib/components/ui/sheet/index";
	import Sidebar from "./Sidebar.svelte";
	import ScreenMode from "./ScreenMode.svelte";
    import logo from "$lib/assets/logo.webp";
</script>
<div class="h-16 grid items-center grid-cols-2 flex-none">
    <div class="col-span-1 pl-4 grid grid-flow-col gap-3">
        <Sheet.Root>
            <Sheet.Trigger asChild let:builder>
                <Button builders={[builder]} size="icon" variant="outline" class="sm:hidden">
                    <PanelLeft class="h-5 w-5" />
                    <span class="sr-only">Toggle Menu</span>
                </Button>
            </Sheet.Trigger>
            <Sheet.Content side="left">
                <Sidebar/>
            </Sheet.Content>
        </Sheet.Root>
        <div class="flex flex-row gap-6">
            <!-- <img alt="(ICON)" src={logo} class="w-10 h-10 rounded-full"/> -->
            <h3 class="scroll-m-20 text-xl font-extrabold tracking-tight algin-bottom">potli</h3>
        </div>
    </div>
    <div class="col-span-1 justify-self-end pr-4">
        <Button variant="link"><a href="https://github.com/Akshay-VK/potli">Github</a></Button>
        <ScreenMode/>
    </div>
</div>