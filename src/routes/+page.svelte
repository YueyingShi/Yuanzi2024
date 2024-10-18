<script>
	// List of chapters for the content
	const chapters = [
		{ id: 1, title: 'Chapter 1', subtitle: 'Long long ago...', audioFile: '/audio/chapter1.mp3' },
		{ id: 2, title: 'Chapter 2', subtitle: 'magic spring...', audioFile: '/audio/chapter2.mp3' }
		// Add more chapters as needed
	];
</script>

<div class="flex flex-col items-center justify-center p-4">
	<img src="/title.png" alt="Chapter title" class="w-56 h-56 mb-4 mt-2" />
	<h1 class="text-3xl font-bold mb-6">Chapters</h1>
	<ul class="w-full max-w-lg">
		{#each chapters as chapter}
			<li class="mb-4">
				<a href={'/' + chapter.id} class="block p-4 bg-white rounded-lg shadow-md hover:bg-gray-50">
					{chapter.title}
				</a>
			</li>
		{/each}
	</ul>
</div>
