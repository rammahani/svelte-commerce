<script>
import Checkbox from '$lib/ui/Checkbox.svelte'
import { constructURL, constructURL2 } from '$lib/util'
import { goto } from '$app/navigation'
import { page } from '$app/stores'
import { Icon, ChevronDown, Adjustments, SortAscending } from 'svelte-hero-icons'
import { createEventDispatcher } from 'svelte'
const dispatch = createEventDispatcher()
export let sortBy = null,
	count = 0,
	showFilters = false,
	searchQuery
let query = {}
// page.subscribe((page) => {
// 	query = JSON.parse(page.url.searchParams.toString())
// })
// function sort() {
// 	query.sort = sortBy
// 	let url = constructURL2('/search', query)
// 	goto(url)
// }
</script>

<div class="flex-none items-center justify-between p-3  text-sm text-gray-800 md:flex md:py-6">
	<div class="flex items-center justify-center p-1 ">
		{#if !count && count != 0}
			<div class="">Will just take a moment...</div>
		{:else}
			<div class="ml-4 ">
				{#if count && count.value != null}
					<span class="font-bold ">{count.value}</span>
				{:else}
					<span class="font-bold ">{count}</span>
				{/if}
				<span class="font-thin">Products found</span>
			</div>
		{/if}
	</div>
	<!-- <div class="flex-wrap hidden p-1 mt-4 md:block">
		<div class="text-sm">
			<div class="relative inline-block">
				<select
					bind:value="{sortBy}"
					class="block px-4 py-2 text-black border border-gray-100 cursor-pointer hover:border-gray-500 focus:outline-none"
					on:change="{sort}">
					{#each sorts as s, ix}
						<option class="bg-white" value="{s.val}">{s.name}</option>
					{/each}
				</select>
				<div
					class="absolute inset-y-0 right-0 flex items-center px-2 text-gray-700 pointer-events-none"
					aria-label="sort">
					<Icon src="{ChevronDown}" class="w-4 px-1" />
				</div>
			</div>
		</div>
	</div> -->
</div>
<div class="mb-1 flex w-full p-2 text-gray-800 shadow md:hidden">
	<button
		class="text-primary flex flex-1 items-center text-left"
		on:click="{() => dispatch('hide', !showFilters)}">
		<Icon src="{Adjustments}" class="w-5 text-gray-800" />
		<span class="tracking wide ml-2 font-medium uppercase ">Filter</span>
	</button>
	<div class="hidden px-4 text-center font-normal text-gray-700 md:block">
		{count} products
	</div>
	<!-- <div class="px-4 text-center text-gray-700 flex">
		<Icon src="{SortAscending}" class="w-4 mr-2 text-primary" />
		<select
			aria-label="sort"
			bind:value="{sortBy}"
			class="flex-1 px-4 py-2 border border-gray-400 cursor-pointer text-primary hover:border-gray-500 focus:outline-none"
			on:input="{sort}">
			{#each sorts as s, ix}
				<option class="bg-white" value="{s.val}">{s.name}</option>
			{/each}
		</select>
	</div> -->
</div>
