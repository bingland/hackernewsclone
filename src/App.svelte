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

	// return base URL for output
	const getHostname = (url) => {
		if (url !== undefined)
			return new URL(url).hostname
		return ''
	}


	// if array length is 30, sort the array by points
	$: if (topStories && topStories.length === 30) {
		topStories.sort((a, b) => { return a.score > b.score ? -1 : 1 })
		topStories.forEach(story => story.baseurl = getHostname(story.url))
		resultsReady = true
	}
</script>

<main>
	<nav>Hacker News Clone</nav>
	{#if resultsReady}
		{#each topStories as story, i}
			<div class="story">
				<div class="line1">
					<div class="rank">#{i + 1}</div> 
					<a class="storyTitle" href="{story.url}">{story.title}</a> 
					<a class="storyUrl" href="{story.url}">({story.url ? story.baseurl : 'No URL returned'})</a>
				</div>
				<div class="line2">{story.score} points by {story.by}</div>
			</div>
		{/each}
	{/if}
</main>

<style>
	main {
		font-family: 'Source Sans Pro', sans-serif;
		max-width: 1200px;
		margin: 5px auto;
		background-color: #F2F2F0;
	}

	nav {
		font-size: 16px;
		background-color: #F25C05;
		font-weight: bold;
		padding: 5px;
		margin-bottom: 5px;
	}

	.story {
		padding: 5px;
		padding-left: 10px;
	}

	.line1 {
		display: flex;
		position: relative;
	}
	.rank {
		color: rgb(78, 78, 78);
		width: 29px;
	}
	.storyTitle {
		color: black;
		text-decoration: none;
		cursor: pointer;
	}
	.storyUrl {
		color: rgb(78, 78, 78);
		padding-left: 4px;
		font-size: 14px;
		position: relative;
		bottom: -2px;
		cursor: pointer;
		text-decoration: none;
	}
	.storyUrl:hover {
		text-decoration: underline;
	}

	.line2 {
		font-size: 13px;
		padding-left: 29px;
		padding-top: 3px;
		color: rgb(78, 78, 78);
	}
</style>