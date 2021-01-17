<script context="module">
	export async function load({ page, context, session }) {
		console.log("Load function called with page", page);
		console.log("Received context",context,"from layout component")
		return { props: { context }}
	}
</script>

<script>
	import { onMount } from 'svelte';
	import { session } from '$app/stores';
	onMount(() => { console.log('onMount() called'); $session = {}; });
	import Counter from '$components/Counter.svelte';

	export let context;
</script>

<main>
	<h1>Hello world!</h1>

	<p>Info received from top-level layout: {context.info}</p>

	<Counter/>
	<p>Visit the <a href="https://svelte.dev">svelte.dev</a> to learn how to build Svelte apps.</p>
</main>

<style lang="scss">
	:root {
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
	}

	main {
		text-align: center;
		padding: 1em;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4rem;
		font-weight: 100;
		line-height: 1.1;
		margin: 4rem auto;
		max-width: 14rem;
	}

	p {
		max-width: 14rem;
		margin: 2rem auto;
		line-height: 1.35;
	}

	@media (min-width: 480px) {
		h1 {
			max-width: none;
		}

		p {
			max-width: none;
		}
	}
</style>