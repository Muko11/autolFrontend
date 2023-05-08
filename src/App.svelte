<script>
	import { setContext } from "svelte";
	import { Router } from "svelte-routing";
	import Nav from "./Nav.svelte";
	import Contenido from "./Contenido.svelte";
	import Footer from "./Footer.svelte";

	/* const URL = {
		usuarios: "http://localhost:3000/autol/usuarios/",
		login: "http://localhost:3000/autol/login/",
		singup: "http://localhost:3000/autol/singup/",
		account: "http://localhost:3000/autol/account/",
		autoescuela: "http://localhost:3000/autol/autoescuela/",
		profesor: "http://localhost:3000/autol/profesor/",
		alumno: "http://localhost:3000/autol/alumno/",
		practica: "http://localhost:3000/autol/practica/",
	}; */

	const URL = {
		usuarios: "https://autolbackend.onrender.com/autol/usuarios/",
		login: "https://autolbackend.onrender.com/autol/login/",
		singup: "https://autolbackend.onrender.com/autol/singup/",
		account: "https://autolbackend.onrender.com/autol/account/",
		autoescuela: "https://autolbackend.onrender.com/autol/autoescuela/",
		profesor: "https://autolbackend.onrender.com/autol/profesor/",
		alumno: "https://autolbackend.onrender.com/autol/alumno/",
		practica: "https://autolbackend.onrender.com/autol/practica/",
	};

	setContext("URL", URL);
</script>

<Router>
	<Nav />
	<Contenido />
	<Footer />
</Router>
