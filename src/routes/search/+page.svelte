<style>
.frosted {
	backdrop-filter: blur(15px);
	background-color: hsla(0, 0%, 100%, 0.75);
}
</style>

<script>
import { goto } from '$app/navigation'
import { constructQry, constructURL2 } from '$lib/util'
import { sorts } from '$lib/config'
import Product from '$lib/Product.svelte'
import Pagination from './_Pagination.svelte'
import ProductSkeleton from './_ProductSkeleton.svelte'
import HeaderBody from './_HeaderBody.svelte'
import NoProduct from './_NoProduct.svelte'
import DesktopFilters from './_DesktopFilters.svelte'
import MobileFilters from './_MobileFilters.svelte'
import ProductCard from '$lib/components/_ProductCard.svelte'
import ProductCardEs from './_ProductCardEs.svelte'
import { get } from '$lib/util/api'

export let data
let { page, products, facets, query, count } = data
$: ({ page, products, facets, query, count } = data)

const PAGE_SIZE = 30
let showMobileFilter = false,
	category = {},
	productCount = 0,
	pageSize = 10,
	currentPage = 1,
	loading = false,
	searchQuery
function changePage(e, p) {
	let fl = { ...query }
	delete fl.page
	const url = constructURL2('/search', fl)
	let page = parseInt(e.detail || 1)
	goto(`${url}page=${page}`)
	// scrollToTop();
}
function toggle(e) {
	showMobileFilter = e.detail
}
</script>

<svelte:head>
	<title>Svelte Ecommerce</title>
	<meta name="description" content="Latest Ecommerce Stack" />
</svelte:head>
{#if showMobileFilter}
	<MobileFilters
		facets="{facets}"
		showMobileFilter="{showMobileFilter}"
		query="{query}"
		on:hide="{toggle}" />
{:else}
	<div class="flex ">
		{#if loading}
			Loading Products...
		{:else if products}
			<DesktopFilters facets="{facets}" query="{query}" />
			<div class="w-full">
				<HeaderBody
					searchQuery="{searchQuery}"
					count="{products.count}"
					on:hide="{() => (showMobileFilter = !showMobileFilter)}" />
				{#if loading}
					<div class="flex flex-wrap justify-between">
						{#each { length: 15 } as _, i}
							<ProductSkeleton />
						{/each}
					</div>
				{:else if !products || !products.length}
					<NoProduct />
				{:else if products && products.length > 0}
					<div class="flex flex-wrap">
						{#each products as p}
							{#if p}
								<div class="w-1/2 lg:w-1/4">
									<ProductCardEs product="{p}" />
								</div>
							{/if}
						{/each}
					</div>
				{/if}
				<Pagination count="{Math.ceil(count / 40)}" current="{+currentPage}" />
			</div>
			<div class="mt-20 lg:hidden">
				<div class="frosted fixed bottom-0 w-full py-2 text-center">
					<span class="font-bold ">{count}</span> products
				</div>
			</div>
		{/if}
	</div>
{/if}
