<script lang="ts">
	import './app.css';
	import { parseIcsEvent, type VEvent } from 'ts-ics';
	import { google, outlook, yahoo, aol } from 'calendar-link';

	let event: VEvent | null = $state(null);
	let fileName = $state('');

	function readFile(file: File) {
		return new Promise<string>((resolve, reject) => {
			const reader = new FileReader();

			reader.onload = () => {
				resolve(reader.result as string);
			};
			reader.onerror = reject;
			reader.readAsText(file);
		});
	}

	async function handleUpload(e: Event) {
		const input = e.target as HTMLInputElement;
		if (input.files?.length !== 1) return;

		const file = input.files[0];
		const content = await readFile(file);

		event = parseIcsEvent(content);
		fileName = file.name;
	}
</script>

<div class="flex min-h-screen flex-col text-gray-900 dark:bg-gray-900 dark:text-gray-50">
	<div class="flex flex-1 flex-col items-center justify-center pt-10">
		<h1 class="p-3 text-center text-3xl font-bold">Convert your .ics file into calendar links</h1>
		<label
			class={'mt-5 flex cursor-pointer justify-center rounded-lg border p-5 ' +
				'ring-amber-500 hover:bg-gray-50 has-focus-visible:ring-4 lg:w-100 ' +
				'dark:ring-amber-300 dark:hover:bg-gray-800 ' +
				(fileName
					? 'border-green-500 bg-green-100 dark:border-green-400 dark:bg-green-900'
					: 'border-dashed')}
		>
			<input type="file" accept=".ics" class="sr-only" onchange={handleUpload} />
			<span class="text-lg">{fileName || 'Upload your .ics file'}</span>
		</label>

		{#if event}
			<div class="mt-5 flex flex-col gap-2">
				<a
					href={google({
						title: event.summary,
						start: event.start.date,
						end: event.end?.date,
						location: event.location,
						description: event.description,
						url: event.url
					})}
					target="_blank"
					rel="noopener noreferrer"
					class="inline-flex gap-2 text-blue-500 underline dark:text-blue-400"
				>
					<img src="https://api.iconify.design/logos:google-calendar.svg" alt="" />
					Add to Google Calendar
				</a>
				<a
					href={outlook({
						title: event.summary,
						start: event.start.date,
						end: event.end?.date,
						location: event.location,
						description: event.description,
						url: event.url
					})}
					target="_blank"
					rel="noopener noreferrer"
					class="inline-flex gap-2 text-blue-500 underline dark:text-blue-400"
				>
					<img src="https://api.iconify.design/logos:microsoft-icon.svg" alt="" />
					Add to Outlook
				</a>
				<a
					href={yahoo({
						title: event.summary,
						start: event.start.date,
						end: event.end?.date,
						location: event.location,
						description: event.description,
						url: event.url
					})}
					target="_blank"
					rel="noopener noreferrer"
					class="inline-flex gap-2 text-blue-500 underline dark:text-blue-400"
				>
					<img
						src="https://api.iconify.design/simple-icons:yahoo.svg"
						alt=""
						class="rounded-sm dark:bg-gray-50 dark:px-1"
					/>
					Add to Yahoo
				</a>
				<a
					href={aol({
						title: event.summary,
						start: event.start.date,
						end: event.end?.date,
						location: event.location,
						description: event.description,
						url: event.url
					})}
					target="_blank"
					rel="noopener noreferrer"
					class="inline-flex gap-2 text-blue-500 underline dark:text-blue-400"
				>
					<img
						src="https://api.iconify.design/simple-icons:aol.svg"
						alt=""
						class="rounded-sm dark:bg-gray-50 dark:px-1"
					/>
					Add to AOL
				</a>
			</div>
			<details class="relative mt-4">
				<summary class="p-2">Raw event JSON</summary>
				<button
					class="absolute right-1 flex -translate-y-full items-center gap-2 rounded-sm bg-gray-200 p-2 px-3 active:bg-gray-300 dark:bg-gray-700 dark:active:bg-gray-600"
					onclick={(event) => {
						navigator.clipboard.writeText(JSON.stringify(event, null, 2));
						event.currentTarget.textContent = 'Copied!';
						setTimeout(() => {
							(event.target as HTMLButtonElement).textContent = 'Copy';
						}, 1000);
					}}
				>
					Copy
				</button>
				<pre class="box-border max-h-80 max-w-screen overflow-auto p-2">{JSON.stringify(
						event,
						null,
						2
					)}</pre>
			</details>
		{/if}
	</div>

	<footer class="m-3 text-center text-sm text-gray-500 dark:text-gray-300">
		No data ever leaves your device.
	</footer>
</div>
