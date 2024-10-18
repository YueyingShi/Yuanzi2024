<script>
	import { onMount, onDestroy } from 'svelte';
	import { Icon, Play, Pause } from 'svelte-hero-icons';

	export let src; // The audio file to be played

	let audio;
	let isPlaying = false;
	let currentTime = 0;
	let duration = 0;

	// Initialize audio on mount
	onMount(() => {
		audio = new Audio(src);
		audio.addEventListener('loadedmetadata', () => {
			duration = audio.duration;
		});
		audio.addEventListener('timeupdate', () => {
			currentTime = audio.currentTime;
		});
		audio.addEventListener('ended', () => {
			isPlaying = false;
		});

		// Autoplay the audio
		audio
			.play()
			.then(() => {
				isPlaying = true; // Update the playing state
			})
			.catch((error) => {
				console.error('Autoplay failed:', error);
				// Handle autoplay failure (e.g., show a message or keep the play button)
			});
	});

	// Clean up the audio when the component is destroyed
	onDestroy(() => {
		if (audio) {
			audio.pause(); // Pause the audio
			audio.currentTime = 0; // Reset the audio time
			isPlaying = false; // Update the playing state
		}
	});

	// Toggle play/pause
	function togglePlayPause() {
		if (isPlaying) {
			audio.pause();
		} else {
			audio.play();
		}
		isPlaying = !isPlaying;
	}

	// Seek to a specific time
	function seek(event) {
		const timeline = event.target;
		const percentage = event.offsetX / timeline.clientWidth;
		audio.currentTime = percentage * duration;
	}
</script>

<div class="flex flex-col w-full items-center gap-2">
	<div class="flex flex-col items-center w-full gap-2">
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<div class="timeline relative cursor-pointer w-full h-4" on:click={seek}>
			<div class="bg-gray-300 rounded h-4 w-full">
				<div
					class="h-full bg-blue-500 rounded"
					style="width: {duration ? (currentTime / duration) * 100 : 0}%; height: 100%;"
				></div>
			</div>
		</div>

		<div class="w-full flex justify-between text-sm font-medium">
			<span>{Math.floor(currentTime)}s</span>
			<!-- Current time in seconds -->
			<span>{Math.floor(duration)}s</span>
			<!-- Duration in seconds -->
		</div>
	</div>

	<!-- Play button -->
	<button
		on:click={togglePlayPause}
		class="play-pause-btn flex items-center justify-center w-16 h-16 bg-blue-500 text-white rounded-full"
	>
		{#if isPlaying}
			<Icon src={Pause} size="32" solid />
		{:else}
			<Icon src={Play} size="32" solid />
		{/if}
	</button>
</div>
