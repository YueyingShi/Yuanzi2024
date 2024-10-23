<script>
	import { onMount, onDestroy } from 'svelte';
	import { Icon, Play, Pause } from 'svelte-hero-icons';

	export let src; // The audio file to be played
	export let audioName;

	let audio;
	let isPlaying = false;
	let currentTime = 0;
	let duration = 0;

	// Initialize audio on mount
	onMount(() => {
		audio = new Audio(src);

		// Set duration when the metadata is loaded
		audio.addEventListener('loadedmetadata', () => {
			duration = audio.duration;
		});

		// Update currentTime as the audio plays
		audio.addEventListener('timeupdate', () => {
			currentTime = audio.currentTime;
		});

		// When audio ends, reset the state
		audio.addEventListener('ended', () => {
			isPlaying = false;
			currentTime = 0;
		});

		// Handle errors and fallback to default audio
		audio.addEventListener('error', () => {
			console.error('Error loading audio, switching to default audio.');
			audio.src = '/audio/default.wav';
			audio.load();
		});

		// Autoplay the audio if possible
		// audio
		// 	.play()
		// 	.then(() => {
		// 		isPlaying = true;
		// 	})
		// 	.catch((error) => {
		// 		console.error('Autoplay failed:', error);
		// 	});
	});

	// Handle visibility change to pause/resume the audio when the tab is not visible
	const handleVisibilityChange = () => {
		if (document.hidden && isPlaying) {
			audio.pause();
			isPlaying = false;
		} else if (!document.hidden && !isPlaying && currentTime > 0) {
			audio.play();
			isPlaying = true;
		}
	};

	document.addEventListener('visibilitychange', handleVisibilityChange);

	// Clean up when the component is destroyed
	onDestroy(() => {
		if (audio) {
			audio.pause();
			audio.currentTime = 0;
		}
		document.removeEventListener('visibilitychange', handleVisibilityChange);
	});

	// Toggle play/pause state
	function togglePlayPause() {
		if (isPlaying) {
			audio.pause();
		} else {
			audio.play();
		}
		isPlaying = !isPlaying;
	}

	// Seek within the audio
	function seek(event) {
		const timeline = event.target;
		const percentage = event.offsetX / timeline.clientWidth;
		audio.currentTime = percentage * duration;
	}
</script>

<div class="flex flex-col w-full gap-2 py-1">
	<!-- Display Audio Name (Assuming audio name is passed as a prop) -->
	<h2 class="text-lg font-medium">{audioName} said,</h2>
	<!-- Add audio name on top -->

	<!-- Container for Play/Pause Button and Seek Bar -->
	<div class="flex items-start w-full gap-3">
		<!-- Play/Pause Button -->
		<button
			on:click={togglePlayPause}
			class="play-pause-btn flex items-center justify-center p-2 bg-blue-500 text-white rounded-full"
		>
			{#if isPlaying}
				<Icon src={Pause} size="16" solid />
			{:else}
				<Icon src={Play} size="16" solid />
			{/if}
		</button>

		<!-- Seek Bar and Time Display next to Play Button -->
		<div class="flex flex-col w-full gap-2 pt-2">
			<!-- Seek Bar -->
			<div class="timeline relative cursor-pointer w-full h-4" on:click={seek}>
				<div class="bg-gray-300 rounded h-4 w-full">
					<div
						class="h-full bg-blue-500 rounded"
						style="width: {duration ? (currentTime / duration) * 100 : 0}%; height: 100%;"
					></div>
				</div>
			</div>

			<!-- Time Display -->
			<div class="w-full flex justify-between text-sm font-medium">
				<span>{Math.floor(currentTime)}s</span>
				<span>{Math.floor(duration)}s</span>
			</div>
		</div>
	</div>
</div>
