<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import AudioPlayer from '$lib/components/AudioPlayer.svelte'; // Updated import
	import { Icon, ArrowLeft } from 'svelte-hero-icons';
	import { capitalizeFirstLetter } from '$lib/utils';
	import { chapters } from '$lib/chapters'; // Adjust the path as necessary
	import { get } from 'svelte/store'; // Needed to directly get store values

	let story: string | undefined;
	let currentChapter: { title: string; subtitle: string } | undefined;
	let title = 'Title not available';
	let subtitle = 'Subtitle not available';
	const defaultAvatar = '/avatars/robot.png'; // Path to your default image
	let avatarSrc = defaultAvatar; // Set to default initially

	// Use the $page param to get the current story once the component mounts
	onMount(() => {
		const pageData = get(page); // Get the current page data from store
		story = pageData.params.story; // Safely access the story parameter

		if (story) {
			// Find the chapter for the current story
			currentChapter = chapters.find((chapter) => chapter.story.includes(story));

			// Update title and subtitle based on the found chapter
			if (currentChapter) {
				title = currentChapter.title;
				subtitle = currentChapter.subtitle;
			}

			// Set the avatarSrc based on the story
			avatarSrc = `/avatars/${story}.png`;
		}
	});

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
			class="w-56 h-56 mb-8 mt-2 mix-blend-darken"
			on:error={() => (avatarSrc = defaultAvatar)}
		/>
		<div class="flex flex-col w-full gap-1">
			<h1 class="text-xl font-medium">{subtitle}</h1>
			<!-- <h2 class="text-xl text-gray-800">{subtitle}</h2> -->
		</div>
		<!-- Simplified Audio Player Component -->
		<AudioPlayer src={`/audio/${story}.wav`} />
	</div>
</div>
