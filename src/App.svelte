<script>
	import { writable } from "svelte/store";

	const JIRA_PREFIX = 'jira-prefix';
	const prefixStore = writable(localStorage.getItem(JIRA_PREFIX));

	export let prefixInput = '';
	export let keysInput = '';

	prefixStore.subscribe(value => {
		prefixInput = value;
		localStorage.setItem(JIRA_PREFIX, value);
	});

	const updatePrefix = () => {
		prefixStore.set(prefixInput);
	}

	const keysToQuery = (str) => `key in (${str.split('\n').map(key => `"${key}"`).join(', ')})`
	const toUrl = (keys, urlPrefix) => `${urlPrefix}?jql=${encodeURIComponent(keysToQuery(keys))}`;
</script>

<main>
	<h1>JIRA Bulk-Edit Helper</h1>

	<code>
		<a
			href="{toUrl(keysInput, prefixInput)}"
			target="_BLANK"
		>{keysToQuery(keysInput)}</a>
	</code>

	<div class="inputs">
		<label>
			URL Prefix (end with <tt>/issues/</tt>):
			<br />
			<input
				bind:value={prefixInput}
				on:change={updatePrefix}
				placeholder="https://something.atlassian.net/issues/"
			>
		</label>
		<label>
			Issue keys (one per line):
			<br />
			<textarea bind:value={keysInput} rows="10"></textarea>
		</label>
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	input, textarea {
		width: 80%;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>