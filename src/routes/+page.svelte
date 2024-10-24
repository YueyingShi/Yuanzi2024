<script>
	import { capitalizeFirstLetter } from '$lib/utils';
	// List of chapters for the content
	import { chapters } from '$lib/chapters'; // Adjust the path as necessary
</script>

<div class="flex flex-col items-center justify-center max-w-lg mt-4">
	<img src="/title.png" alt="Chapter title" class="w-full h-auto mb-8 mix-blend-darken" />

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
