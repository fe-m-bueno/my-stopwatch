<script lang="ts">
	import Stopwatch from '$lib/Stopwatch.svelte';
	import HistoryList from '$lib/HistoryList.svelte';
	import ThemeToggle from '$lib/ThemeToggle.svelte';
	import Footer from '$lib/Footer.svelte';
	import { History, X } from 'lucide-svelte';
	let showHistory = false;

	function toggleHistory() {
		showHistory = !showHistory;
	}
</script>

<main class="bg-test flex min-h-screen items-center justify-center">
	<div class="fixed left-12 top-8">
		<ThemeToggle />
	</div>
	<div class="w-full max-w-md">
		<Stopwatch />
	</div>
	{#if !showHistory}
		<button
			onclick={toggleHistory}
			class="fixed right-12 top-8 z-10 rounded-full bg-gray-100 p-2 shadow-md lg:hidden dark:bg-[#242424]"
		>
			<History size={20} />
		</button>
	{/if}

	<div
		class="fixed inset-y-0 right-0 z-10 w-full transform bg-gray-200 shadow-lg transition-transform duration-300 dark:bg-slate-950
      {showHistory ? 'translate-x-0' : 'translate-x-full'} lg:hidden"
	>
		<button onclick={toggleHistory} class="fixed right-12 top-8 p-2">
			<X size={20} />
		</button>
		<HistoryList />
	</div>

	<div
		class="absolute right-0 top-1/2 hidden h-full -translate-y-1/2 overflow-hidden overflow-y-scroll shadow-xl lg:block"
	>
		<HistoryList />
	</div>
	<div class="fixed bottom-10">
		<Footer />
	</div>
</main>

<style>
	html,
	body {
		height: 100%;
		overflow: hidden;
	}
</style>
