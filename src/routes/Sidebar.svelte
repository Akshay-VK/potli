<script>
	import ScrollArea from "$lib/components/ui/scroll-area/scroll-area.svelte";
import SiteLink from "./SiteLink.svelte";

</script>

<ScrollArea class="h-4/5 mt-8 ">
	<div class="flex flex-col justify-items-start gap-1 h-screen mt-4 mb-4 overflow-hidden">
		<SiteLink text="Home" to="/"/>
		<SiteLink text="Test" to="/test"/>
	</div>
</ScrollArea>