<script>
import Pricesummary from '$lib/components/Pricesummary.svelte'
import SelectAddress from '../_SelectAddress.svelte'
import { GQL_myAddresses, GQL_me, GQL_cart } from '$houdini'
import Error from '$lib/Error.svelte'
import CheckoutHeader from '../_CheckoutHeader.svelte'
import { browser } from '$app/env'

export let data
let { loading, myAddresses, err, prescriptionId, selectedAddress } = data
$: ({ loading, myAddresses, err, prescriptionId, selectedAddress } = data)

$: browser && GQL_myAddresses.fetch()

$: myAddresses = $GQL_myAddresses.data?.myAddresses
function addressChanged(detail) {
	// console.log('detail = ', detail)
	selectedAddress = detail.detail
}
async function refreshAddress() {
	try {
		await GQL_myAddresses.fetch({ settings: { cacheMs: 0 } })
	} catch (e) {
		err = e
	} finally {
	}
}
</script>

<div class="container mx-auto w-full max-w-6xl p-3 py-5 text-gray-800 sm:p-10">
	<Error err="{err}" />

	<CheckoutHeader selected="address" />

	<div class="mt-5 md:mt-10 lg:flex lg:justify-center lg:space-x-10 xl:space-x-20">
		<div class="w-full flex-1">
			<h2 class="text-xl font-bold capitalize tracking-wide sm:text-2xl">
				Select Delivery Address
			</h2>

			{#if myAddresses?.count > 0}
				<div class="mx-auto mt-5 rounded-lg border bg-white shadow-lg">
					{#each myAddresses.data as ads}
						<SelectAddress
							loading="{loading}"
							address="{ads}"
							selectedAddress="{selectedAddress}"
							on:deleteAddress="{refreshAddress}"
							on:addressChanged="{({ detail }) => addressChanged({ detail })}" />
					{/each}
				</div>
			{:else}
				<hr class="mt-5" />
			{/if}

			<div class="my-10 w-1/2">
				<a
					href="/checkout/add-address?id=new"
					class="group flex h-40 w-full flex-col items-center justify-center rounded-md border border-dashed border-gray-400 hover:border-primary-500 sm:h-60 ">
					<div
						class="flex h-8 w-8 items-center justify-center rounded-full border  border-gray-400 bg-gray-100 group-hover:border-primary-500 sm:h-10 sm:w-10">
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-6 w-6 text-gray-600 group-hover:text-primary-500"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 6v6m0 0v6m0-6h6m-6 0H6"></path>
						</svg>
					</div>

					<span
						class="mt-2 text-sm font-medium text-gray-800 group-hover:text-primary-500 sm:text-base">
						ADD NEW ADDRESS
					</span>
				</a>
			</div>
		</div>

		<div class="w-full lg:w-80 lg:flex-shrink-0 lg:flex-grow-0">
			<h2 class="text-xl font-bold capitalize tracking-wide sm:text-2xl">Cart Summary</h2>
			{#if !selectedAddress}
				<Pricesummary text="Please select address" loading="{loading}" />
			{:else}
				<Pricesummary
					text="Proceed"
					nextpage="
				{prescriptionId
						? `/checkout/payment-options?address=${selectedAddress}&prescription=${prescriptionId}`
						: `/checkout/payment-options?address=${selectedAddress}`}"
					loading="{loading}" />
			{/if}
		</div>
	</div>
</div>
