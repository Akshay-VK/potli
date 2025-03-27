<script>
	import { Slider } from '$lib/components/ui/slider';
	import { Label } from '$lib/components/ui/label';
	import ScrollArea from '$lib/components/ui/scroll-area/scroll-area.svelte';
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
		{ label: 'GMT', value: 'UTC' },
		{ label: 'PST', value: 'America/Los_Angeles' },
		{ label: 'EST', value: 'America/New_York' },
		{ label: 'CET', value: 'Europe/Paris' },
		{ label: 'CET', value: 'Europe/Berlin' },
		{ label: 'MSK', value: 'Europe/Moscow' },
		{ label: 'AST', value: 'Asia/Dubai' },
		{ label: 'IST', value: 'Asia/Kolkata' },
		{ label: 'CST', value: 'Asia/Shanghai' },
		{ label: 'JST', value: 'Asia/Tokyo' }
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

	let test = 2;
</script>

{#snippet content()}
	<div class="">
		<h1 class="scroll-m-20 pb-4 text-3xl font-extrabold tracking-tight lg:text-4xl">
			Time zone converter
		</h1>

		<div class="w-full py-4">
			<Popover.Root>
				<Popover.Trigger asChild let:builder>
					<Button
						variant="outline"
						class={cn(
							'w-[280px] justify-start text-left font-normal',
							!formvalue && 'text-muted-foreground'
						)}
						builders={[builder]}
					>
						<CalendarIcon class="mr-2 h-4 w-4" />
						{formvalue ? df.format(selectedDate) : 'Pick a date'}
					</Button>
				</Popover.Trigger>
				<Popover.Content class="w-auto p-0">
					<Calendar
						bind:value={
							() => fromDate(selectedDate, selectedTimeZone), (v) => (selectedDate = v.toDate())
						}
						initialFocus
					/>
				</Popover.Content>
			</Popover.Root>
		</div>

		<div class="w-full py-4">
			<!-- <Label for="email">Select Time Zone:</Label> -->
			<Select.Root
				selected={selectedTimeZone}
				onSelectedChange={(v) => {
					console.log(v);
					selectedTimeZone = v ? v.value : timeZoneOptions[0].value;
				}}
			>
				<Select.Trigger class="w-64">
					<Select.Value placeholder="Time Zone" />
				</Select.Trigger>
				<Select.Content class="">
					<ScrollArea class="h-64">
						{#each timeZoneOptions as option}
							<Select.Item value={option.value}
								>{'' +
									option.value +
									' ' +
									(option.label.length > 0 ? '(' + option.label + ')' : '')}</Select.Item
							>
						{/each}
					</ScrollArea>
				</Select.Content>
			</Select.Root>
		</div>

		<!-- <div class="py-2">
			<p class="text-sm font-semibold text-muted-foreground">Adjust offset</p>
		</div>
		<div class="w-64 px-4 py-4">
		
		</div> -->
		<!-- <input type="range" min="0" max="1440" bind:value={offset} /> -->
		<div class="py-2 text-lg font-semibold">
			<p class="text-sm text-muted-foreground py-2">Time: <code class="scroll-m-20 text-2xl font-semibold tracking-tight px-4">{('' + Math.floor(offset / 60)).padStart(2, '0')}:{('' + (offset % 60)).padStart(2, '0')}</code></p>
			
			<div class="w-64 py-4 px-4">
				<Slider
					value={[offset]}
					max={1440}
					step={1}
					onValueChange={(v) => {
						offset = v[0];
					}}
				/>
				<p class="text-muted-foreground text-xs py-4">Drag to adjust time.</p>
			</div>
		</div>
		<div class="py-4">
			<div class="text-lg font-semibold">
				<p class="text-sm text-muted-foreground">Converted time:</p>
				<code class="scroll-m-20 text-2xl font-semibold tracking-tight">
					{updateTimeZone(adjustedDate, selectedTimeZone)}
				</code>
			</div>
		</div>
	</div>
{/snippet}

<Base {content} title='Time zone converter'/>
