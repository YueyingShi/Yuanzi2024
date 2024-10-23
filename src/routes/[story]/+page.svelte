<script>
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import AudioPlayer from '$lib/components/AudioPlayer.svelte'; // Updated import
	import { Icon, ArrowLeft } from 'svelte-hero-icons';
	import { capitalizeFirstLetter } from '$lib/utils';

	// Get the chapter ID from the URL
	$: story = $page.params.story;

	// Sample subtitles based on the chapter

	const titles = {
		trainers: '琪琪， 田哥和小游说:',
		animals: 'Ruirui, Jiejie 和 熊熊说:'
		// Add more subtitles for each chapter
	};

	const subtitles = {
		trainers: 'I wanna kill you',
		animals: 'A Deeper Dive'
		// Add more subtitles for each chapter
	};

	$: title = titles[story] || 'Subtitle not available';
	$: subtitle = subtitles[story] || 'Subtitle not available';

	function goBack() {
		goto('/');
	}
</script>

<div class="flex flex-col w-full h-[70vh] items-center gap-12">
	<!-- Back Button -->
	<div class="w-full flex">
		<button on:click={goBack} class=" flex items-center gap-2 text-gray-800">
			<Icon src={ArrowLeft} size="20" solid />
			<p class=" text-gray-700">{capitalizeFirstLetter(story)}</p>
		</button>
	</div>

	<!-- chapter information -->
	<div class="flex flex-col w-full h-full items-center justify-center gap-4">
		<!-- Avatar image -->
		<img
			src="/avatars/{story}.png"
			alt=" {story} avatar"
			class="w-56 h-56 mb-4 mt-2 mix-blend-darken"
		/>
		<div class="flex flex-col w-full gap-1 mb-8">
			<h1 class="text-xl font-medium">{title}</h1>
			<!-- <h2 class="text-xl text-gray-800">{subtitle}</h2> -->
		</div>
		<!-- Simplified Audio Player Component -->
		<AudioPlayer src={`/audio/${story}.wav`} />
	</div>
</div>
