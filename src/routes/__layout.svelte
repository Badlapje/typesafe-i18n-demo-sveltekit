<script context="module" lang="ts">
	import { initI18n } from '$i18n/i18n-svelte'
	import type { Load } from '@sveltejs/kit'
	import type { Locales } from '$i18n/i18n-types'
	import { replaceLocaleInUrl } from '../utils'
	import { baseLocale, locales } from '$i18n/i18n-util'
	import type { SessionPayload } from '../hooks'

	type LoadInput = {
		pageParams: { lang: Locales }
		session: SessionPayload
	}

	export const load: Load<LoadInput> = async ({ url, session, params }) => {
		const lang = params.lang

		// redirect to preferred language if user comes from page root
		if (!lang) {
			return {
				status: 302,
				redirect: `/${session.locale}`,
			}
		}

		// redirect to base locale if language is not present
		if (!locales.includes(lang)) {
			return {
				status: 302,
				redirect: replaceLocaleInUrl(url.pathname, baseLocale),
			}
		}

		// delete session locale since we don't need it to be sent to the client
		delete session.locale

		await initI18n(lang)

		return {}
	}
</script>

<script lang="ts">
	import Header from '$lib/Header.svelte'
</script>

<Header />

<main>
	<slot />
</main>

<style lang="scss" global>
	@import '../styles/global.scss';
</style>
