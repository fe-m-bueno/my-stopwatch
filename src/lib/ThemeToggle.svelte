<script lang="ts">
	import { onMount } from 'svelte';
	import { Sun, Moon } from 'lucide-svelte';

	let isDark: boolean;

	function toggleTheme() {
		isDark = !isDark;
		document.documentElement.setAttribute('data-mode', isDark ? 'dark' : 'light');
		localStorage.setItem('theme', isDark ? 'dark' : 'light');

		document.documentElement.classList.toggle('dark', isDark);
	}

	onMount(() => {
		const savedTheme = localStorage.getItem('theme') || 'light';
		isDark = savedTheme === 'dark';
		document.documentElement.setAttribute('data-mode', savedTheme);
		document.documentElement.classList.toggle('dark', isDark);
	});
</script>

<button
	onclick={toggleTheme}
	class="relative flex h-8 w-16 items-center rounded-full border border-gray-200 bg-gray-100 p-1 transition-colors dark:border-gray-400 dark:bg-[#242424]"
	aria-label="Theme Toggle"
>
	<div
		class="absolute h-6 w-6 transform rounded-full bg-white shadow-md transition-transform duration-300 dark:bg-slate-950"
		class:translate-x-0={!isDark}
		class:translate-x-8={isDark}
	>
		{#if isDark}
			<Moon class="m-1 h-4 w-4 text-white" />
		{:else}
			<Sun class="m-1 h-4 w-4 " />
		{/if}
	</div>
</button>
