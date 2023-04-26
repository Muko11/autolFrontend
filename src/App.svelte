<script>
	import { setContext } from "svelte";
	import { Router } from "svelte-routing";
	import Nav from "./Nav.svelte";
	import Contenido from "./Contenido.svelte";
	import Footer from "./Footer.svelte";

	const URL = {
		usuarios: "http://localhost:3000/autol/usuarios/",
		login: "http://localhost:3000/autol/login/",
		singup: "http://localhost:3000/autol/singup/",
		account: "http://localhost:3000/autol/account/",
	};

	setContext("URL", URL);
</script>

<Router>
	<Nav />
	<Contenido />
	<Footer />
</Router>