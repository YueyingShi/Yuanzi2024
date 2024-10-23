<script>
	import { capitalizeFirstLetter } from '$lib/utils';
	// List of chapters for the content
	import { chapters } from '$lib/chapters'; // Adjust the path as necessary
</script>

<div class="flex flex-col items-center justify-center p-4">
	<img src="/title.png" alt="Chapter title" class="w-56 h-56 mb-4 mt-2 mix-blend-darken" />
	<!-- <h1 class="text-3xl font-bold mb-6">Chapters</h1> -->
	<ul class="w-full max-w-lg">
		{#each chapters as chapter}
			<li class="mb-4">
				<a
					href={'/' + chapter.story}
					class="block p-4 bg-white rounded-lg shadow-md hover:bg-gray-50"
				>
					{capitalizeFirstLetter(chapter.title)}
				</a>
			</li>
		{/each}
	</ul>
</div>
