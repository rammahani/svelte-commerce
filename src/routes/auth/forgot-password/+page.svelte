<style>
.frosted {
	background-image: url('/auth/login/bg-lighter.svg');
	backdrop-filter: blur(15px);
	background-color: hsla(0, 0%, 100%, 0.75);
}
</style>

<script>
import { goto } from '$app/navigation'
import GradiantButton from '$lib/ui/GradiantButton.svelte'
import TextboxFloating from '$lib/ui/TextboxFloating.svelte'
import { toast } from '$lib/util'
import SEO from '$lib/components/SEO/index.svelte'
import ImageLoader from '$lib/components/Image/ImageLoader.svelte'
import { post } from '$lib/util/api'
import { GQL_emailPassword, GQL_storeOne } from '$houdini'
import { loginUrl } from '$lib/store'
import { onMount } from 'svelte'

export let data
let { store } = data
$: ({ store } = data)
const seoProps = {
	title: 'Forgot Password ',
	metadescription: 'Forgot Password'
}

onMount(async () => {
	// await GQL_storeOne.fetch({ variables: { id: store?.id } })
})

let loading = false
let email, referrer
async function handleForgotPassword() {
	try {
		toast('Sending password reset email...', 'info')
		loading = true
		referrer = 'https://atoz-chi.vercel.app/api'
		const emailPassword = await GQL_emailPassword.mutate({ variables: { email, referrer } })
		if (!emailPassword.errors) {
			toast(`Email sent to ${email}`, 'success')
			goto('/auth/forgot-success')
		}
	} catch (e) {
		toast(e, 'error')
	} finally {
		loading = false
	}

	// signInPromise = forgotPassword({ email, password, name, lastName, role })
}
</script>

<SEO {...seoProps} />

<div
	class="frosted container mx-auto flex w-full max-w-sm flex-col rounded-2xl border bg-cover bg-center bg-no-repeat px-4 py-10 shadow-2xl sm:px-8"
	style="background-image: url('/login/bg-lighter.svg');">
	<!-- Store logo/name  -->

	<a href="/" class="mx-auto mb-5 max-w-max">
		{#if store?.logoCdn}
			<ImageLoader
				src="{store?.logoCdn}"
				alt="{store?.name} logo"
				class="h-10 w-auto flex-shrink-0 object-contain object-center" />
		{:else if store?.websiteName}
			<h1
				class="bg-gradient-to-br from-secondary-500 to-primary-500 bg-clip-text text-4xl font-extrabold text-transparent">
				{store?.websiteName}
			</h1>
		{/if}
	</a>

	<div class="mb-5 text-center font-bold uppercase">FORGOT PASSWORD</div>

	<h6 class="mb-3 text-center text-gray-600">We will email you the password reset link.</h6>

	<form class="mb-8" on:submit|preventDefault="{handleForgotPassword}">
		<TextboxFloating bind:value="{email}" class="mb-5 w-full" type="email" required label="Email" />

		<GradiantButton class="mx-auto w-full max-w-sm" type="submit" loading="{loading}">
			SEND EMAIL
		</GradiantButton>
	</form>

	<div class="flex items-center justify-center">
		<a
			href="{$loginUrl}?ref=/auth/forgot-password"
			class="max-w-max text-start text-primary-500 hover:underline">
			Back to login
		</a>
	</div>
</div>
