<script>
    import { Route } from "svelte-routing";
    import Inicio from "./Inicio.svelte";
    import Profesor from "./Profesor.svelte";
    import Alumno from "./Alumno.svelte";
    import Singup from "./Singup.svelte";
    import Login from "./Login.svelte";
  </script>
  
  <main>
    <!-- Se eliminan etiquetas html para resaltar lo esencial -->
    <!-- Consulta el código fuente. --> 
    <Route path="/" component={Inicio} />
    <Route path="/login" component={Login} />
    <Route path="/singup" component={Singup} />
    <Route path="/profesor" component={Profesor} />
    <Route path="/alumno" component={Alumno} />
  </main>
  
  <style>
    /* Aquí el código CSS */
    /* Consultar el código fuente */
  </style>