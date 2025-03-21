<script lang="ts">
    import { Button } from "$lib/components/ui/button/index";
    import { Input } from "$lib/components/ui/input/index";
    import { ScrollArea } from "$lib/components/ui/scroll-area/index";
    import { Separator } from "$lib/components/ui/separator/index";
    import * as Select from "$lib/components/ui/select";

	import Base from "../Base.svelte";

    let split = $state(false);
    let final_split: {a:string,b:string,amount:number}[]=[];

    type Payment={
        name: string;
        amount: number;
    }

    let newname=$state("");
    let newpmt=$state({name:"",amount:0} as Payment);
    let users: string[] = $state([] as string[]);
    let payments = $state([] as Payment[]);

    function add_user(){
        // console.log(newname);
        users.push(newname);
        // console.log(users);
        newname="";
    }
    function remove_user(i:number){
        users.splice(i,1);
    }

    function add_pmt(){
        // newpmt.amount=parseFloat(newpmt.amount.lsplit("0")[0]);
        // console.log(newpmt);
        payments.push({name:newpmt.name,amount:parseFloat(newpmt.amount+"")});
        newpmt.amount=0;
    }
    function remove_pmt(i:number){
        payments.splice(i,1);
    }

    function splitMoney(){
        let map: Record<string,Record<string,number>> = {};

        //initialising entries to 0
		for (let i = 0; i < users.length; i++) {
			let index = '' + users[i];
			map[index] = {};
			for (let j = 0; j < users.length; j++) {
				if (i != j) {
					map[index]['' + users[j]] = 0;
				}
			}
		}
		let n = users.length;
		// console.log(map);
		/*
        Each sub name/sub-key's value is how much that person has to pay to the key
        {
            a:{
                b:5
            },
            b:{
                a:10
            }
        }
        This means that b should pay a 5 and a should pay b 10.
        */
		for (let i = 0; i < payments.length; i++) {
			let k = Object.keys(map[payments[i].name]);
			let v = payments[i].amount;
			for (let j = 0; j < k.length; j++) {
				map[payments[i].name][k[j]] += v / n;
			}
		}
		// console.log(map);
		/*
        Create a array []
        Iterate as above and on each step, add to the array {a:first,b:second:amount:amount}
        amount is the difference of occurences at map[first][second] and map[second][first]
        Therefore this check has to be made if the given pair has been iterated upon before or no
        */

		let k = Object.keys(map);
		for (let i = 0; i < k.length; i++) {
			let k2 = Object.keys(map[k[i]]);
			for (let j = 0; j < k2.length; j++) {
				// map[payments[i].name][k[j]]+=v/n;

				let a: string = k[i];
				let to_a: number = map[k[i]][k2[j]];
				let b: string = k2[j];
				let to_b: number = map[b][a];
				let obj: { a: string; b: string; amount: number; } = {
					a: a,
					b: b,
					//what a must give to b
					amount: to_b - to_a
				};
				//the opposite that we should check for
				let opp: { a: string; b: string; amount: number; } = {
					a: b,
					b: a,
					amount: to_a - to_b
				};
				if (final_split.filter((e) => e.a == b && e.b == a).length > 0) {
					continue;
				} else {
					final_split.push(obj);
				}
			}
		}
		console.log(final_split);
        
        split=true;
    }

</script>

{#snippet content()}
<h1 class="scroll-m-20 pb-8 text-3xl font-extrabold tracking-tight lg:text-4xl">
    Money Splitter
</h1>
<!-- ADD/REMOVE USERS -->
<div class="py-2 w-4/5">
    <h4 class="scroll-m-20 text-xl font-semibold tracking-tight py-4">People</h4>

    <form class="flex flex-col sm:flex-row w-full items-center space-x-0 space-y-3 sm:space-y-0 sm:space-x-2">
        <Input class="sm:flex-auto" bind:value={newname} placeholder="Add person..." />
        <Button class="sm:flex-none w-full sm:w-fit" on:click={()=>{add_user();newname==""}} disabled={newname.length==0}>Add</Button>
    </form>
    <div class="py-4">
        <ScrollArea class="h-[200px] w-full rounded-md border p-4">
            {#if users.length>0}
                {#each users as user,i}
                    <div class="flex justify-between py-2 place-items-center">
                        <p class="font-semibold">{user}</p>
                        <Button variant="destructive" on:click={()=>remove_user(i)} class="w-fit">Remove</Button>
                    </div>
                    {#if i<users.length-1}
                        <Separator/>
                    {/if}
                {/each}
            {:else}
                <div class="h-[150px] grid place-content-center">
                <p class="text-center text-muted-foreground text-xl">No users added</p>                
                </div>
            {/if}
        </ScrollArea>
    </div>
</div>
<Separator class="my-4 w-4/5" />
<!-- ADD/REMOVE TRANSACTIONS -->
<div class="py-2 w-4/5">
    <h4 class="scroll-m-20 text-xl font-semibold tracking-tight py-4">Payments</h4>

    <form class="flex flex-col sm:flex-row w-full items-center space-x-0 space-y-3 sm:space-y-0 sm:space-x-2">
        <Select.Root
            onSelectedChange={(v) => {
                if (!v){
                    return;
                }
                newpmt.name = v?.value as string;
          }}
          disabled={users.length==0}
          >
            <Select.Trigger class="sm:w-[180px] flex-auto">
              <Select.Value placeholder="Select person paying..." />
            </Select.Trigger>
            <Select.Content>
                {#each users as user}
                    <Select.Item value={user}>{user}</Select.Item>
                {/each}
            </Select.Content>
          </Select.Root>
        <p class="font-semibold">paid</p>
        <Input class="sm:flex-auto" bind:value={newpmt.amount} placeholder="0" disabled={users.length==0}/>
        <Button class="sm:flex-none w-full sm:w-fit" on:click={()=>add_pmt()} disabled={newpmt.name.length==0 || newpmt.amount==0}>Add</Button>
    </form>
    <div class="py-4">
        <ScrollArea class="h-[200px] w-full rounded-md border p-4">
            {#if payments.length>0}
                <div class="h-[150px]">
                    {#each payments as pmt,i}
                        <div class="flex justify-between py-2 place-items-center">
                            <p class="font-semibold">{pmt.name} <i>paid</i> {pmt.amount}</p>
                            <Button variant="destructive" on:click={()=>remove_pmt(i)} class="w-fit">Remove</Button>
                        </div>
                        {#if i<users.length-1}
                            <Separator/>
                        {/if}
                    {/each}               
                </div>
            {:else}
                <div class="h-[150px] grid place-content-center">
                    <p class="text-center text-muted-foreground text-xl">No payments added</p>                
                </div>
            {/if}
        </ScrollArea>
    </div>
</div>
<Separator class="my-4 w-4/5" />
<!-- FINAL SPLIT -->
<div class="py-4 w-4/5">
<Button class="w-full" disabled={users.length==0 || payments.length==0} on:click={()=>splitMoney()}>Split Money</Button>
</div>

{#if split}
    <ScrollArea class="h-[200px] w-4/5 rounded-md border p-4">
        <div class="h-[150px]">
            {#each final_split as s,i}
                {#if s.amount>0}
                    <div class="flex justify-between py-2 place-items-center">
                        <p class="font-semibold">{s.a} <i>must pay</i> {s.amount} <i>to</i> {s.b}</p>
                    </div>
                {:else if s.amount<0}
                    <div class="flex justify-between py-2 place-items-center">
                        <p class="font-semibold">{s.b} <i>must pay</i> {-s.amount} <i>to</i> {s.a}</p>
                    </div>
                {/if}
                {#if i<users.length-1}
                    <Separator/>
                {/if}
            {/each}               
        </div>
    </ScrollArea>
    <p class="text-muted-foreground text-sm py-4">Need to make a new split? Just hit refresh. 😝</p>
{/if}

{/snippet}

<Base {content}/>