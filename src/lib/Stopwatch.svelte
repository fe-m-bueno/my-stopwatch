<script lang="ts">
	let time = $state<number>(283);
	let isRunning = $state<boolean>(false);
	let showModal = $state<boolean>(false);
	let intervalId: ReturnType<typeof setInterval> | null = null;

	function start() {
		if (!isRunning) {
			isRunning = true;
			intervalId = setInterval(() => {
				time++;
			}, 1000);
		}
	}

	function stop() {
		if (isRunning) {
			isRunning = false;
			if (intervalId !== null) {
				clearInterval(intervalId);
				intervalId = null;
			}
		}
	}

	function reset() {
		if (time > 0) {
			saveTime(time);
		}
		time = 0;
		stop();
	}

	function toggle() {
		isRunning ? stop() : start();
	}

	function toggleModal() {
		showModal = !showModal;
	}

	const formatTime = $derived.by(() => {
		const hours: number = Math.floor(time / 3600);
		const minutes: number = Math.floor((time % 3600) / 60);
		const seconds: number = time % 60;
		return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
	});

	$effect(() => {
		function handleKeydown(event: KeyboardEvent) {
			if (event.code === 'Space') {
				event.preventDefault();
				toggle();
			} else if (event.key.toLowerCase() === 's') {
				reset();
			} else if (event.key.toLowerCase() === '?' || (event.code === 'Escape' && showModal)) {
				toggleModal();
			}
		}

		window.addEventListener('keydown', handleKeydown);
		return () => window.removeEventListener('keydown', handleKeydown);
	});

	function closeModal(event: MouseEvent) {
		// Fecha o modal APENAS se o clique for no fundo (não nos botões internos)
		if (event.target === event.currentTarget) {
			toggleModal();
		}
	}
	function handleKey(event: KeyboardEvent) {
		if (event.key === 'Escape' && showModal) {
			toggleModal();
		}
	}

	const saveTime = (timeOnReset: number): void => {
		const now = new Date();
		const date = now.toLocaleDateString();
		const hour = now.toLocaleTimeString();

		const record = {
			time: timeOnReset,
			date,
			hour
		};

		const storedTimes = JSON.parse(localStorage.getItem('timeRecords') || '[]');
		storedTimes.push(record);
		localStorage.setItem('timeRecords', JSON.stringify(storedTimes));
		window.dispatchEvent(new Event('storage-update'));
	};
	const buttonText = $derived.by(() => {
		if (isRunning) return 'Pause';
		return time > 0 ? 'Continue' : 'Start';
	});

	$effect(() => {
		document.title = isRunning ? formatTime : 'Stopwatch';

		const favicon = document.querySelector("link[rel~='icon']") as HTMLLinkElement;
		if (favicon) {
			favicon.href = isRunning ? '/favicon2.png' : '/favicon.png';
		}
	});
</script>

<div class="flex w-full max-w-lg flex-col items-center space-y-10">
	<h1 class="text-[clamp(3rem,6vw,4rem)] font-bold">Stopwatch</h1>
	<div class="text-[clamp(5rem,12vw,8rem)]">{formatTime}</div>
	<div class="flex flex-row items-center space-x-4">
		<button
			onclick={toggle}
			class="cursor-pointer rounded bg-secondary-600 px-4 py-2 text-white transition hover:bg-secondary-700 dark:bg-secondary-800 dark:hover:bg-secondary-700"
		>
			{buttonText}
		</button>
		<button
			onclick={reset}
			class={`rounded  px-4 py-2 text-white transition ${isRunning ? 'cursor-pointer bg-danger-600 transition hover:bg-danger-700 dark:bg-danger-800 dark:hover:bg-danger-700' : 'cursor-not-allowed bg-gray-500 transition dark:bg-gray-700'}`}
			disabled={!isRunning}>Stop</button
		>
	</div>
</div>

<div class="fixed bottom-8 right-[19rem] z-10 hidden lg:block">
	<button
		onclick={toggleModal}
		class="flex h-12 w-12 cursor-pointer items-center justify-center rounded-full bg-slate-100 px-2 py-2 text-black shadow-md transition hover:bg-slate-300 dark:bg-gray-400 dark:hover:bg-gray-500"
	>
		?
	</button>
</div>

<!-- Modal -->
{#if showModal}
	<div
		class="fixed inset-0 z-10 flex items-center justify-center bg-white/30"
		onclick={closeModal}
		onkeydown={handleKey}
		tabindex="-1"
		role="dialog"
		aria-modal="true"
		aria-labelledby="modal-title"
	>
		<div
			class="flex w-full max-w-md flex-col items-center justify-center rounded-2xl bg-white p-6 shadow-lg dark:bg-slate-950"
		>
			<h2 class="mb-4 text-2xl font-bold">Shortcuts</h2>
			<ul class="p-1">
				<li class="mb-5">
					<span
						class="mr-3 rounded-md border border-gray-200 bg-gray-100 px-2 py-2 font-mono shadow dark:bg-gray-900 dark:shadow-white"
						>Space</span
					> Start/pause the timer
				</li>
				<li class="mb-5">
					<span
						class="mr-3 rounded-md border border-gray-200 bg-gray-100 px-2 py-2 font-mono shadow dark:bg-gray-900 dark:shadow-white"
						>s</span
					> Stop the timer
				</li>
				<li class="mb-5">
					<span
						class="mr-1 rounded-md border border-gray-200 bg-gray-100 px-2 py-2 font-mono shadow dark:bg-gray-900 dark:shadow-white"
						>?</span
					>

					Open/close shortcuts window
				</li>
				<li class="mb-5">
					<span
						class="mr-3 rounded-md border border-gray-200 bg-gray-100 px-2 py-2 font-mono shadow dark:bg-gray-900 dark:shadow-white"
						>Esc</span
					> Close shortcuts window
				</li>

				<li class="mb-5">
					<span
						class="mr-3 rounded-md border border-gray-200 bg-gray-100 px-2 py-2 font-mono shadow dark:bg-gray-900 dark:shadow-white"
						>c</span
					> Clean history
				</li>
			</ul>
			<button
				onclick={toggleModal}
				class="mt-4 rounded bg-danger-500 px-4 py-2 text-white transition hover:bg-danger-600 dark:bg-danger-700 dark:hover:bg-danger-600"
			>
				Close
			</button>
		</div>
	</div>
{/if}
