<script>
	import favicon from '$lib/assets/favicon.svg';
	import '../app.css'

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200&icon_names=cannabis,pets,sunny_snowing" /> 
</svelte:head>

{@render children()}
