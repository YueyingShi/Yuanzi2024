<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import AudioPlayer from '$lib/components/AudioPlayer.svelte';
	import { Icon, ArrowLeft } from 'svelte-hero-icons';
	import { capitalizeFirstLetter } from '$lib/utils';
	import { chapters } from '$lib/chapters'; // Adjust the path as necessary
	import { get } from 'svelte/store';

	let story: string | undefined;
	let currentChapter: { title: string; subtitle: string; audios: string[] } | undefined;
	let title = 'Title not available';
	let subtitle = 'Subtitle not available';
	const defaultAvatar = '/avatars/robot.png'; // Path to your default image
	let avatarSrc = defaultAvatar; // Set to default initially
	let audios: string[] = []; // To store audio sources for the current story
	const audioFormats = ['wav', 'mp3', 'm4a']; // Supported audio formats

	onMount(() => {
		const pageData = get(page); // Get the current page data from store
		story = pageData.params.story; // Safely access the story parameter

		if (story) {
			// Find the chapter for the current story
			currentChapter = chapters.find((chapter) => chapter.story.includes(story));

			// Update title, subtitle, and audio based on the found chapter
			if (currentChapter) {
				title = currentChapter.title;
				subtitle = currentChapter.subtitle;
				audios = currentChapter.audios || []; // Store the audios array
			}

			// Set the avatarSrc based on the story
			avatarSrc = `/avatars/${story}.png`;
		}
	});

	function getAudioFile(audioBaseName: string): Promise<string> {
		return new Promise((resolve, reject) => {
			for (const format of audioFormats) {
				const audioPath = `/audio/${story}/${audioBaseName}.${format}`;
				const audio = new Audio(audioPath);

				audio.addEventListener('canplaythrough', () => {
					resolve(audioPath); // Resolve with the path if the format is playable
				});

				audio.addEventListener('error', () => {
					console.warn(`Format ${format} not supported or audio file missing: ${audioPath}`);
				});

				// Try loading the audio (it will trigger either 'canplaythrough' or 'error')
				audio.load();
			}

			// If no format is playable, reject the promise
			setTimeout(() => {
				reject('No playable audio format found.');
			}, 1000); // Timeout for format detection
		});
	}

	async function loadAudio(audioBaseName: string) {
		try {
			return await getAudioFile(audioBaseName);
		} catch (error) {
			console.error(error);
			return ''; // Return empty if no format is playable
		}
	}

	function goBack() {
		goto('/');
	}
</script>

<div class="flex flex-col w-full min-h-[70vh] items-center gap-12">
	<!-- Back Button -->
	<div class="w-full flex">
		<button on:click={goBack} class="flex items-center gap-2 text-gray-800">
			<Icon src={ArrowLeft} size="20" solid />
			<p class="text-gray-700">{capitalizeFirstLetter(story)}</p>
		</button>
	</div>

	<!-- Chapter Information -->
	<div class="flex flex-col w-full h-full items-center justify-center gap-4">
		<!-- Avatar image -->
		<img
			src={avatarSrc}
			alt=" {story} avatar"
			class="w-56 h-56 mb-8 mt-2 mix-blend-darken"
			on:error={() => (avatarSrc = defaultAvatar)}
		/>
		<!-- <div class="flex flex-col w-full gap-1">
			<h1 class="text-xl font-medium">{subtitle}</h1>
		</div> -->

		<!-- Audio Players for Multiple Audio Files -->
		{#each audios as audioBaseName}
			{#await loadAudio(audioBaseName) then audioSrc}
				{#if audioSrc}
					<AudioPlayer src={audioSrc} audioName={capitalizeFirstLetter(audioBaseName)} />
				{/if}
			{:catch error}
				<p class="text-red-500">Audio unavailable</p>
			{/await}
		{/each}

		<!-- Picture of wished -->
		<div class="flex flex-col w-full {story?.includes('ending') ? '' : 'hidden'}">
			<img
				src="/wishimg/tiantian wishs.jpg"
				alt=" tiantian wishs"
				class="w-full h-auto max-w-xl mb-8 mt-2 mix-blend-darken"
				on:error={() => (avatarSrc = defaultAvatar)}
			/>
			<img
				src="/wishimg/peter wishs.jpg"
				alt=" peter wishs"
				class="w-full h-auto max-w-xl mb-8 mt-2 mix-blend-darken"
				on:error={() => (avatarSrc = defaultAvatar)}
			/>
			<img
				src="/wishimg/title.png"
				alt="ending"
				class="w-full h-auto max-w-xl mb-8 mt-2 mix-blend-darken"
				on:error={() => (avatarSrc = defaultAvatar)}
			/>
		</div>
	</div>
</div>
