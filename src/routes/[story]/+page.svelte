<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import AudioPlayer from '$lib/components/AudioPlayer.svelte'; // Updated import
	import { Icon, ArrowLeft } from 'svelte-hero-icons';
	import { capitalizeFirstLetter } from '$lib/utils';
	import { chapters } from '$lib/chapters'; // Adjust the path as necessary

	// Use the $page param to get the current story
	$: story = $page.params.story;

	// Find the chapter for the current story
	const currentChapter = chapters.find((chapter) => chapter.story === story);
	$: title = currentChapter ? currentChapter.title : 'Title not available';
	$: subtitle = currentChapter ? currentChapter.subtitle : 'Subtitle not available';

	const defaultAvatar = '/avatars/robot.png'; // Path to your default image
	let avatarSrc = `/avatars/${story}.png`; // Initially set to the dynamic source

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
			src={avatarSrc}
			alt=" {story} avatar"
			class="w-56 h-56 mb-4 mt-2 mix-blend-darken"
			on:error={() => (avatarSrc = defaultAvatar)}
		/>
		<div class="flex flex-col w-full gap-1 mb-8">
			<h1 class="text-xl font-medium">{subtitle}</h1>
			<!-- <h2 class="text-xl text-gray-800">{subtitle}</h2> -->
		</div>
		<!-- Simplified Audio Player Component -->
		<AudioPlayer src={`/audio/${story}.wav`} />
	</div>
</div>
