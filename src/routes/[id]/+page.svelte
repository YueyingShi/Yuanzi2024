<script>
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import AudioPlayer from '$lib/components/AudioPlayer.svelte'; // Updated import
	import { Icon, ArrowLeft } from 'svelte-hero-icons';
	// Get the chapter ID from the URL
	$: id = $page.params.id;

	// Sample subtitles based on the chapter

	const titles = {
		1: 'Dragon says:',
		2: 'Animals say:'
		// Add more subtitles for each chapter
	};

	const subtitles = {
		1: 'I wanna kill you',
		2: 'A Deeper Dive'
		// Add more subtitles for each chapter
	};

	$: title = titles[id] || 'Subtitle not available';
	$: subtitle = subtitles[id] || 'Subtitle not available';

	function goBack() {
		goto('/');
	}
</script>

<div class="flex flex-col w-full h-[70vh] items-center gap-12">
	<!-- Back Button -->
	<div class="w-full flex">
		<button on:click={goBack} class=" flex items-center gap-2 text-gray-800">
			<Icon src={ArrowLeft} size="20" solid />
			<p class="text-lg text-gray-700">Chapters/ Chapter{id}</p>
		</button>
	</div>

	<!-- chapter information -->
	<div class="flex flex-col w-full h-full items-center justify-center gap-4">
		<!-- Avatar image -->
		<img
			src="/avatars/chapter{id}.png"
			alt="Chapter {id} avatar"
			class="w-40 h-40 rounded-full mb-4 border border-gray-300 mt-2"
		/>
		<div class="flex flex-col w-full gap-1 mb-8">
			<h1 class="text-xl font-medium">{title}</h1>
			<h2 class="text-xl text-gray-800">{subtitle}</h2>
		</div>
		<!-- Simplified Audio Player Component -->
		<AudioPlayer src={`/audio/chapter${id}.mp3`} />
	</div>
</div>
