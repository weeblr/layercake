<script context="module">
	export const prerender = true;
	export async function load({ page, fetch }) {
		const url = `${page.params.slug}.json`;
		const res = await fetch(url);
		const data = await res.json();

		if (res.status === 200) {
			return {
				props: {
					slug: page.params.slug,
					data,
					active: 'index'
				}
			};
		} else {
			return {
				status: res.status,
				error: new Error(`Could not load ${url}: ${data.message}`)
			}
		}
	}
</script>

<script>
	import MarkdownIt from 'markdown-it';
	import hljs from 'highlight.js';

	import DownloadBtn from '../_site-components/DownloadBtn.svelte';
	import hljsDefineSvelte from '../../_modules/hljsDefineSvelte.js';
	import cleanTitle from '../../_modules/cleanTitle.js';

	import examples from '../_examples.js';

	const md = new MarkdownIt({
		html: true,
		linkfify: true
	});

	hljs.registerLanguage('svelte', hljsDefineSvelte);
	hljsDefineSvelte(hljs);

	export let slug;
	export let data;


	export let active = 'index';

	function markdownToHtml (text) {
		return md.render(text);
	}

	function highlight (str, title) {
		const parts = title.split('.');
		let ext = parts[parts.length - 1];
		if (ext === 'csv') ext = 'diff'
		return hljs.highlight(str, { language: ext }).value;
	}

	$: pages = [data.main]
		.concat(data.components)
		.concat(data.componentModules)
		.concat(data.modules)
		.concat(data.componentComponents)
		.concat(data.jsons)
		.concat(data.csvs);

	const exampleLookup = new Map();
	examples.forEach(exmpl => {
		exampleLookup.set(exmpl.slug, exmpl);
	});

	$: example = exampleLookup.get(slug);

	function copyToClipboard () {
		const text = pages.filter(d => cleanTitle(d.title) === active)[0].contents;
		if (window.clipboardData && window.clipboardData.setData) {
			return window.clipboardData.setData('Text', text);
		} else if (document.queryCommandSupported && document.queryCommandSupported('copy')) {
			const textarea = document.createElement('textarea');
			textarea.textContent = text;
			textarea.style.position = 'fixed';
			document.body.appendChild(textarea);
			textarea.select();
			try {
				return document.execCommand('copy');
			} catch (ex) {
				console.warn('Copy to clipboard failed.', ex);
				return false;
			} finally {
				document.body.removeChild(textarea);
			}
		}
	}

</script>

<style>
	.main {
		position: relative;
		max-width: 54em;
		background-color: white;
		padding: 1.5em 2em 2em 2em;
		margin: 0 auto;
		box-sizing: border-box;
		overflow: hidden;
	}

	.chart-hero {
		width: 100%;
		height: 200px;
		margin: 1.5em 0 2em 0;
		position: relative;
	}

	.dek {
		width: calc(100% - 80px);
	}

	.dek :global(p a) {
		color: #ff3e00;
		text-decoration: none;
	}

	.dek :global(p a:hover) {
		text-decoration: underline;
	}

	#pages {
		margin-top: 50px;
	}

	#pages.has-dek {
		margin-top: 35px;
	}

	.dek {
		width: calc(100% - 80px);
	}

	.dek :global(p a) {
		color: #ff3e00;
		text-decoration: none;
	}

	.dek :global(p a:hover) {
		text-decoration: underline;
	}

	#contents-container {
		position: relative;
		border-left: 3px solid #ccc;
		margin-top: 7px;
	}

	pre {
		margin-top: 7px 0 0 0;
		padding-left: 14px;
		overflow-x: auto;
	}

	#page-nav {
		margin: 0;
		padding: 0;
	}

	.tab {
		display: inline-block;
		vertical-align: top;
		margin-right: 14px;
		margin-bottom: 8px;
		border-bottom: 2px solid transparent;
		color: #ccc;
	}

	.tab:hover {
		border-bottom: 2px solid #aaa;
		cursor: pointer;
	}

	:global(.tab.active) {
		color: #000;
		pointer-events: none;
		border-bottom: 2px solid #000;
	}


	.copy {
		position: absolute;
		top: 0;
		right: 0;
		width: 20px;
		height: 35px;
		opacity: 0.25;
		background-image: url(/copy.svg);
		background-repeat: no-repeat;
		background-size: contain;
		cursor: pointer;
	}

	.copy:hover {
		opacity: 1;
	}

	.copy:active {
		opacity: 0.7;
	}

	.copy:hover:before {
		display: block;
	}

	.edit-repl {
		text-decoration: none !important;
		/* font-size: 12px; */
		text-transform: lowercase;
		font-family: monospace;
		color: rgba(0,0,0,0.5);
		background-color: #f0f0f0;
		padding: 2px 5px;
		margin-left: 7px;
		font-size: 19px;
		vertical-align: top;
		position: relative;
		top: 4px;
	}

	.edit-repl:hover {
		text-decoration: underline;
		color: #ff3e00;
	}

	.copy:before {
		content: 'Copy to clipboard';
		position: absolute;
		top: -7px;
		right: 0;
		background-color: #000;
		border-radius: 2px;
		color: #fff;
		display: none;
		font-size: 13px;
		padding: 3px 5px;
		white-space: nowrap;
		transform: translate(0%, -100%);
	}

	@media (max-width: 750px) {
		.copy {
			transform: translate(0, -80%);
		}
	}
	@media (max-width: 475px) {
		#pages {
			margin-top: 21px;
		}
		.tab {
			margin-top: 8px;
			margin-bottom: 0;
		}
		.download {
			display: none;
		}
		.dek {
			width: 100%;
		}
	}
</style>

<svelte:head>
	<title>{example.title}</title>
</svelte:head>

<div class="main">
	<h1>{example.title}<a class="edit-repl" href="https://svelte.dev/repl/{example.replPath}" target="_blank" rel="nofollow">Edit</a></h1>

	<div class="chart-hero">
		<svelte:component this={example.component} />
	</div>

	<div class="download">
		<DownloadBtn
			{data}
			slug='{slug}'
		/>
	</div>

	{#if data.dek}
		<div class="dek">
			{@html markdownToHtml(data.dek)}
		</div>
	{/if}

	<div id="pages" class="{data.dek ? 'has-dek' : ''}">
		<ul id="page-nav">
			{#each pages as page}
				<li
					class="tab {active === cleanTitle(page.title) ? 'active' : ''}"
					on:click="{() => active = cleanTitle(page.title)}"
				>{page.title}</li>
			{/each}
		</ul>
		<div id="contents-container">
			<div
				class="copy"
				on:click={copyToClipboard}
			></div>
			{#each pages as page}
				<div class="contents" style="display: {active === cleanTitle(page.title) ? 'block' : 'none'};">
					<pre>{@html highlight(page.contents, page.title)}</pre>
				</div>
			{/each}
		</div>
	</div>

</div>
