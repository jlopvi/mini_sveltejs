<script>
	// import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';

	import MovieItem from './Movie/Item.svelte';

	const APIKEY = '29ed1d64cc3508c30f08131eb1860d99';
	const BASEURL = `https://api.themoviedb.org/3`;
	const APISETTINGS = `?api_key=${APIKEY}&language=es-MX`;


	// function fetchMovies () {
	// 	const URL = `${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`

	// 	fetch(URL)
	// 		.then(res => res.json())
	// 		.then(({results})  => {
	// 			movies = results

	// 			console.log(movies)
	// 		})
	// }

	const movies = (async () => {
		const URL = `${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
		const response = await fetch(URL);
		
		return await response.json();
	})()

	let likedMovies = [];

	function toggleLike (event) {
		const movie = event.detail;

		let index = likedMovies.findIndex(m => m.id === movie.id);

		if(index >= 0) {
			likedMovies.splice(index, 1);
			console.log(likedMovies);
		} else {
			likedMovies.push(movie);
		}

		likedMovies = likedMovies
	}

	$: like = (id) => {
		let index = likedMovies.findIndex(m => m.id === id)

		return index >= 0
	}
	// onMount(() => {
	// 	console.log('the component has mounted');
	// 	fetchMovies()
	// });
</script>

<svelte:head>
	<title>Movies Svelte</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</svelte:head>

<main class="conatiner">
	<div class="row">
		<div class="col-12 col-md-6 col-lg-8 border panel ">
			<h2>Peliculas populares</h2>
			<!-- {#each movies as movie}
				<div class="col-12 col-md-6 col-lg-3 p-2">
					<MovieItem id={movie.id} title={movie.title} overview={movie.overview} cover={movie.poster_path}/>
				</div>
			{/each} -->
			<div class="row">
				{#await movies}
					<!-- promise is pending -->
					<div class="col-12">
						<p>Cargando Datos...</p>
					</div>
				{:then data}
					{@debug data}
					<!-- promise was fulfilled -->
					{#each data.results as movie}
						<div class="col-12 col-md-6 col-lg-4 p-2">
							<MovieItem like={like(movie.id)} id={movie.id} title={movie.title} overview={movie.overview} cover={movie.poster_path} on:onToggleLike={toggleLike}/>
						</div>
					{/each}
				{/await}
			</div>
		</div>
		<div class="col-12 col-md-6 col-lg-4 border panel">
			<h2>Peliculas favoritas</h2>
			<div class="row">
				{#if likedMovies.length}
					{#each likedMovies as movie, i (movie.id)}
						<div in:fly="{{duration: 200, y: 20}}" out:fly="{{duration: 800, y: -20}}" class="col-12 col-md-6 col-lg-4 p-2">
							<MovieItem like={like(movie.id)} id={movie.id} title={movie.title} overview="" cover={movie.cover} on:onToggleLike={toggleLike}/>
						</div>
					{/each}
				{:else}
					<div class="col-12">
						<p>No tienes peliculas favoritas...</p>
					</div>
				{/if}

			</div>
		</div>
	</div>
</main>

<style>
	.panel{
		height: 100vh;
		overflow: auto;
	}
	main {
		text-align: center;
		padding: 0;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>