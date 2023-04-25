<script>
	import { setContext } from "svelte";
	import { Router } from "svelte-routing";
	import Nav from "./Nav.svelte";
	import Contenido from "./Contenido.svelte";

	const URL = {
		/* articulos: "https://tiendabackend.fly.dev/api/articulos/",
		clientes: "https://tiendabackend.fly.dev/api/clientes/", */
	};

	setContext("URL", URL);
</script>

<Router>
	<Nav />
	<Contenido />
</Router>

<style>

</style>
