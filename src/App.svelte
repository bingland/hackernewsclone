<script>
	import { onMount } from 'svelte'

	const topStoriesURL = 'https://hacker-news.firebaseio.com/v0/topstories.json'

	let resultsReady = false
	let topStories = []

	const getByID = async (id) => {
		fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json`)
			.then(response => response.json())
			.then(data => {
				console.log(data)
				topStories = [...topStories, data];
			})
	}

	onMount(async () => {
		fetch(topStoriesURL)
			.then(response => response.json())
			.then(data => {
				console.log(data)
				for (let i = 0; i < 30; i++) {
					getByID(data[i])
				}
			})
	})

	// if array length is 30, sort the array by points
	$: if (topStories && topStories.length === 30) {
		topStories.sort((a, b) => { return a.score > b.score ? -1 : 1 })
		resultsReady = true
	}
</script>

<main>
	<h1>Hackernews Clone</h1>
	{#if resultsReady}
		{#each topStories as story}
			<p>{story.title}</p>
		{/each}
	{/if}
</main>

<style>
	main {
		text-align: left;
		padding: 1em;
	}

	h1 {
		font-size: 4em;
		font-weight: 100;
	}
</style>