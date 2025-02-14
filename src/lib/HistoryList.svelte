<script lang="ts">
	let records = $state<{ time: number; date: string; hour: string }[]>([]);

	function loadRecords(): void {
		const storedData = localStorage.getItem('timeRecords');
		if (storedData) {
			records = JSON.parse(storedData);
		} else {
			records = [];
		}
	}

	function updateRecords(): void {
		loadRecords();
	}

	$effect(() => {
		loadRecords();
		const updateListener = () => loadRecords();
		window.addEventListener('storage-update', updateListener);
		return () => window.removeEventListener('storage-update', updateListener);
	});

	function formatTime(time: number): string {
		const hours: number = Math.floor(time / 3600);
		const minutes: number = Math.floor((time % 3600) / 60);
		const seconds: number = time % 60;
		return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
	}

	// Função para limpar os registros
	function clearRecords(): void {
		localStorage.removeItem('timeRecords');
		records = [];
		window.dispatchEvent(new Event('storage-update'));
	}

	$effect(() => {
		function handleKeydown(event: KeyboardEvent) {
			if (event.key.toLowerCase() === 'c') {
				event.preventDefault();
				clearRecords();
			}
		}

		window.addEventListener('keydown', handleKeydown);
		return () => window.removeEventListener('keydown', handleKeydown);
	});
</script>

<div
	class="flex h-full min-h-screen flex-col items-center overflow-y-auto border-white/30 bg-white p-4 pt-10 dark:border-l-2 dark:bg-slate-950"
>
	<h2 class="mb-2 text-xl font-bold">History</h2>
	{#if records.length > 0}
		<ul class="space-y-2">
			{#each records as record, index}
				<li
					class="flex items-center justify-between gap-8 rounded bg-gray-100 px-4 py-2 dark:bg-gray-800"
					role="listitem"
				>
					<span class="text-blue-600 dark:text-blue-200">{formatTime(record.time)}</span>
					<span class="text-sm text-gray-700 dark:text-gray-200">{record.date}</span>
				</li>
			{/each}
		</ul>
		<button
			onclick={clearRecords}
			class="mt-4 cursor-pointer rounded bg-danger-600 px-4 py-2 text-white transition hover:bg-danger-700 dark:bg-danger-800 dark:hover:bg-danger-700"
			aria-labelledby="theme-toggle"
		>
			Clean History
		</button>
	{:else}
		<p
			class="text-md flex flex-wrap items-center justify-center text-center text-slate-900 dark:text-white"
		>
			You haven’t tracked any time yet.
		</p>
	{/if}
</div>
