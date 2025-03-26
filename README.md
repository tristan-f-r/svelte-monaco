# [svelte-monaco](https://tristan-f-r.github.io/svelte-monaco)

[![npm](https://img.shields.io/npm/v/svelte-monaco)](https://npmjs.com/package/svelte-monaco)

Reactive two-way, full theme support monaco editor for Svelte(&Kit).

```sh
pnpm install --save-dev svelte-monaco
```

## Example

```svelte
<script lang="ts">
	// if you want to get themes, feel free to import exportedThemes, nativeThemes, or themeNames.
	import Monaco from 'svelte-monaco';

	// this is fully reactive! setting value to another string will change the editor accordingly
	let value = 'const x = 5';
</script>

<div id="editor">
	<!-- event.detail is the monaco instance. All options are reactive! -->
	<Monaco
		options={{ language: 'typescript', automaticLayout: true }}
		theme="vs-dark"
		on:ready={(event) => console.log(event.detail)}
		bind:value
	/>
</div>

<textarea bind:value />
```

## Features

- Reactive everywhere - two-way value binding with preserved cursor position, and reactive options.
- In-built support for [monaco-themes](https://github.com/brijeshb42/monaco-themes)
- Full access - the moment the editor is finished loading, it is shown to the user. No more waiting for API changes.
