<style>
.h-rem {
	height: 100rem;
}
@media (min-width: 1280px) {
	.h-rem {
		height: 70rem;
	}
}
</style>

<script>
import Skeleton from '$lib/ui/Skeleton.svelte'
import Error from '$lib/components/Error.svelte'
import ImageLoader from '$lib/components/Image/ImageLoader.svelte'
import { GQL_megamenu } from '$houdini'
import SEO from '$lib/components/SEO/index.svelte'
import HeadingUnderline from '$lib/components/HeadingUnderline.svelte'
import { onMount } from 'svelte'
const seoProps = {
	title: 'Sitemap Details',
	metadescription: 'Sitemap Details'
}
onMount(async () => {
	await GQL_megamenu.fetch({})
})

export let data
let { err } = data
$: ({ err } = data)
</script>

<SEO {...seoProps} />

<Error err="{err}" />

<div class="min-h-screen bg-white">
	<div class="container mx-auto max-w-7xl p-3 py-5 sm:p-10">
		<div
			class="mx-auto mb-5 flex max-w-max flex-col items-center justify-center text-gray-800 sm:mb-10">
			<h1 class="mb-2 text-xl font-bold text-primary-500 sm:text-2xl lg:text-3xl">
				All Categories
			</h1>

			<HeadingUnderline />
		</div>

		{#if $GQL_megamenu.isFetching}
			<div class="min-h-screen">
				<Skeleton />
			</div>
		{:else if $GQL_megamenu.data?.megamenu}
			<div>
				<div class="h-rem flex flex-col flex-wrap content-center items-start">
					{#each $GQL_megamenu.data?.megamenu as m}
						<div class="w-1/2 p-2.5 sm:mr-10 sm:mb-10 sm:w-1/4 lg:w-60">
							<a href="/search?q={m.slug}">
								<div
									class="mb-3 font-medium sm:text-lg
									{m.children && m.children.length ? 'border-b border-gray-300 pb-3' : ''}">
									{m.name}
								</div>
							</a>

							{#if m && m.children}
								<div class="flex flex-col space-y-2">
									{#each m.children as mm}
										<a href="/search?q={mm.slug}">
											<div class="text-sm font-light">
												{mm.name}
											</div>
										</a>
									{/each}
								</div>
							{/if}
						</div>
					{/each}
				</div>
			</div>
		{:else}
			<div class="flex items-center justify-center" style="height: 60vh;">
				<div class="m-10 flex flex-col items-center justify-center space-y-5">
					<div>
						<ImageLoader
							src="/no/no-data-availible.png"
							alt="No data availible"
							class="h-20 w-20 object-contain text-xs" />
					</div>

					<p class="text-center text-gray-500">No data found</p>
				</div>
			</div>
		{/if}
	</div>
</div>
