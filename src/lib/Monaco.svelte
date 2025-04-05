<script module lang="ts">
	export const exportedThemes = Object.fromEntries(
		Object.entries(import.meta.glob('/node_modules/monaco-themes/themes/*.json')).map(([k, v]) => [
			k.toLowerCase().split('/').reverse()[0].slice(0, -'.json'.length).replaceAll(' ', '-'),
			v
		])
	);

	export const nativeThemes = ['vs', 'vs-dark', 'hc-black'];

	export const themeNames: string[] = [...Object.keys(exportedThemes), ...nativeThemes].sort(
		(a, b) => a.localeCompare(b)
	);
</script>

<script lang="ts">
	import type MonacoE from 'monaco-editor';
	import { onDestroy, onMount } from 'svelte';
	import { createEventDispatcher } from 'svelte';
	import loader from '@monaco-editor/loader';

	let monaco: typeof MonacoE | undefined = $state();

	const dispatch = createEventDispatcher<{
		ready: MonacoE.editor.IStandaloneCodeEditor;
	}>();

	let container: HTMLDivElement | undefined = $state();

	interface Props {
		editor?: MonacoE.editor.IStandaloneCodeEditor | undefined;
		value: string;
		theme?: string | undefined;
		options?: MonacoE.editor.IStandaloneEditorConstructionOptions;
	}

	let {
		editor = $bindable(undefined),
		value = $bindable(),
		theme = undefined,
		options = {
			value,
			automaticLayout: true
		}
	}: Props = $props();

	function refreshTheme() {
		if (theme) {
			if (exportedThemes[theme]) {
				const themeName = theme; // the theme name can change during the async call
				exportedThemes[theme]().then((resolvedTheme) => {
					monaco?.editor.defineTheme(themeName, resolvedTheme as any);
					monaco?.editor.setTheme(themeName);
				});
			} else if (nativeThemes.includes(theme)) {
				monaco?.editor.setTheme(theme);
			}
		}
	}

	$effect(() => {
		if (theme) refreshTheme();
	});

	$effect(() => {
		editor?.updateOptions(options);
	});

	let model = $derived(editor?.getModel());
	
	$effect(() => {
		(model && options.language) ? monaco!.editor.setModelLanguage(model, options.language) : void 0;
	});

	$effect(() => {
		if (editor && editor.getValue() != value) {
			const position = editor.getPosition();
			editor.setValue(value);
			if (position) editor.setPosition(position);
		}
	});

	onMount(async () => {
		monaco = await loader.init();
		editor = monaco.editor.create(container!, options);

		dispatch('ready', editor);

		refreshTheme();

		editor.getModel()!.onDidChangeContent(() => {
			if (!editor) return;
			value = editor.getValue();
		});
	});

	onDestroy(() => editor?.dispose());
</script>

<div class="monaco-container" bind:this={container}></div>

<style>
	div.monaco-container {
		width: 100%;
		height: 100%;
		padding: 0;
		margin: 0;
	}
</style>
