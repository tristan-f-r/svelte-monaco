<script lang="ts">
	import './style.css';
	import '@fontsource-variable/manrope';

	import Monaco, { themeNames } from '$lib/index';

	let value = $state('const x = 5');
	let readOnly = $state(false);
	let theme = $state('vs-dark');
	let language = $state('typescript');

	function selectTheme(themeSelection: string) {
		return () => {
			theme = themeSelection;
			window.scrollTo(0, 0);
		};
	}
</script>

<main>
	<h1>svelte-monaco</h1>
	<h2>
		monaco bindings for <a href="https://svelte.dev/">Svelte</a> &
		<a href="https://kit.svelte.dev/">SvelteKit</a>
	</h2>

	<p>
		view on: <a href="https://github.com/LeoDog896/svelte-monaco">GitHub</a> |
		<a href="https://npmjs.com/package/svelte-monaco">npm</a>
	</p>

	<div id="playground">
		<div id="editor">
			<Monaco
				options={{
					language,
					automaticLayout: true,
					readOnly
				}}
				{theme}
				bind:value
				on:ready={(editor) => {
					editor.detail.addAction({
						// An unique identifier of the contributed action.
						id: 'my-unique-id',

						// A label of the action that will be presented to the user.
						label: 'Custom Action',

						contextMenuGroupId: 'navigation',

						contextMenuOrder: 1.5,

						// Method that will be executed when the action is triggered.
						// @param editor The editor instance is passed in as a convenience
						run: function (ed) {
							alert("i'm running => " + ed.getPosition());
						}
					});
				}}
			/>
		</div>
		<textarea bind:value></textarea>
	</div>

	<h2>Options</h2>
	<p>
		<b>NOTE</b>: Options can be any JSON object that is passed straight to monaco. This, for
		demonstration, only shows the advanced theme option, and a readonly option.
	</p>

	<!-- readonly checkbox -->
	<label>
		<input type="checkbox" bind:checked={readOnly} />
		readonly
	</label>

	<br />

	<p>Theme: <input type="text" bind:value={theme} /></p>
	<p>Language: <input type="text" bind:value={language} /></p>

	<p>Available themes:</p>
	<div id="themes">
		{#each themeNames as buttonTheme}
			<button onclick={selectTheme(buttonTheme)} class:active={theme === buttonTheme}>
				{buttonTheme}
			</button>
		{/each}
	</div>
</main>

<style>
	main {
		margin: 2rem;
		width: calc(100% - 4rem);
		text-align: center;
	}

	div#editor {
		width: 50%;
		margin-right: 2rem;
		height: 100%;
		border: 1px solid black;
	}

	#playground {
		height: 60vh;
		width: 100%;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		text-align: left;
	}

	textarea {
		width: 50%;
		resize: none;
	}

	#themes {
		display: flex;
		flex-direction: row;
		justify-content: left;
		flex-wrap: wrap;
	}

	#themes button {
		margin: 0.5rem;
		background: white;
		border: 1px solid black;
		padding: 0.5rem;
	}

	#themes button.active {
		background: black;
		color: white;
	}
</style>
