<script>
	import { Slider } from '$lib/components/ui/slider';
	import { Label } from '$lib/components/ui/label';
	import * as Select from '$lib/components/ui/select';

	import Base from '../Base.svelte';
	// @ts-ignore
	import { DateTime } from 'luxon';
	import { fromDate } from '@internationalized/date';

	import CalendarIcon from 'lucide-svelte/icons/calendar';
	import { DateFormatter } from '@internationalized/date';
	import { cn } from '$lib/utils';
	import { Button } from '$lib/components/ui/button/index';
	import { Calendar } from '$lib/components/ui/calendar/index';
	import * as Popover from '$lib/components/ui/popover/index';
	const df = new DateFormatter('en-US', {
		dateStyle: 'long'
	});


    //Date is selected on page load, no need to update it every second.
	let selectedDate = new Date();
	let timeZoneOptions = [
		{ label: 'IST', value: 'Asia/Kolkata' },
		{ label: 'EST', value: 'America/New_York' },
		{ label: 'PST', value: 'America/Los_Angeles' }
		// Add more time zones as needed
	];
	let selectedTimeZone = timeZoneOptions[0].value;
    //offset is in minutes
	let offset = 0;

	// @ts-ignore
	function updateTimeZone(date, timezone) {
        //Convert the date to luxon DateTime object and set the timezone
		return DateTime.fromJSDate(date).setZone(timezone).toLocaleString(DateTime.DATETIME_FULL);
	}

    //Calculate the adjusted date based on the offset
	$: adjustedDate = new Date(
		selectedDate.getTime() -
			(selectedDate.getMilliseconds() +
				selectedDate.getSeconds() * 1000 +
				selectedDate.getMinutes() * 60000 +
				selectedDate.getHours() * 3600000) +
			offset * 60000
	);
    // convert date to formvalue tosatisfy Calender component
	$: formvalue = fromDate(selectedDate, selectedTimeZone);

</script>

{#snippet content()}
	<div class="">
		<h1 class="scroll-m-20 text-3xl font-extrabold tracking-tight lg:text-4xl pb-8">
            Time zone converter
        </h1>

        <div class="w-full py-4">
		<Popover.Root>
			<Popover.Trigger asChild let:builder>
				<Button
					variant="outline"
					class={cn('w-[280px] justify-start text-left font-normal',!formvalue && 'text-muted-foreground')}
					builders={[builder]}
				>
					<CalendarIcon class="mr-2 h-4 w-4" />
					{formvalue ? df.format(selectedDate) : 'Pick a date'}
				</Button>
			</Popover.Trigger>
			<Popover.Content class="w-auto p-0">
				<Calendar bind:value={
                    ()=>fromDate(selectedDate, selectedTimeZone),
                    (v)=>selectedDate=v.toDate()
                    } initialFocus />
			</Popover.Content>
		</Popover.Root>
        </div>

        <div class="w-full py-4">
		<!-- <Label for="email">Select Time Zone:</Label> -->
		<Select.Root
            selected={selectedTimeZone}
            onSelectedChange={(v) => {
                console.log(v);
                selectedTimeZone = v.value;
              }}
            >
			<Select.Trigger class="w-[180px]">
				<Select.Value placeholder="Time Zone" />
			</Select.Trigger>
			<Select.Content>
				{#each timeZoneOptions as option}
					<Select.Item value={option.value}>{option.label}</Select.Item>
				{/each}
			</Select.Content>
		</Select.Root>
        </div>

		<div class="py-2">
            <p class="text-muted-foreground text-sm">Adjust offset</p>
        </div>
		<!-- <Slider value={[offset]} max={120} step={1}/> -->
		<input type="range" min="0" max="3600" bind:value={offset} />
		<div class="text-lg font-semibold">
            <p class="text-muted-foreground text-sm">Offset:</p>
            <h3 class="scroll-m-20 text-2xl font-semibold tracking-tight">{Math.floor(offset / 60)}:{offset % 60}:00</h3>
        </div>
		<div class="py-2">
			<div class="text-lg font-semibold">
				<p class="text-muted-foreground text-sm">Converted time:</p>
                <h3 class="scroll-m-20 text-2xl font-semibold tracking-tight">{updateTimeZone(adjustedDate, selectedTimeZone)}</h3>
                
			</div>
		</div>
	</div>
{/snippet}

<Base {content} />
