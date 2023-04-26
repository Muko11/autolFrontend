<script>
  import { Link } from "svelte-routing";
  import { onMount } from "svelte";
  import { getContext } from "svelte";

  const URL = getContext("URL");

  let usuario = {};

  onMount(async () => {
    const response = await fetch(URL.usuarios + "" );
    if (response.ok) {
      usuario = await response.json();
    }
  });
</script>

<div class="container my-5">
  <Link to="/" class="boton my-4">⬅ Volver</Link>
</div>

<div class="container formulario">
  <p class="fs-3">Bienvenid@, {usuario.nombre}</p>
  <p class="text-muted">Datos de tu cuenta</p>

  <div class="card">
    <div class="card-body">
      <h5 class="card-title">{usuario.nombre} {usuario.apellidos}</h5>
      <p class="card-text"><b>Tipo de cuenta:</b> {usuario.rol}</p>
      <p class="card-text"><b>Correo electrónico:</b> {usuario.correo}</p>
    </div>
  </div>
</div>

<style>
  body {
    background-color: #f5f5f5;
  }

  .container.formulario {
    background-color: white;
    box-shadow: 0px 0px 20px 3px rgba(0, 0, 0, 0.5);
    padding: 40px;
    margin-top: 80px;
  }

  .formulario p {
    margin-bottom: 20px;
  }

  .formulario a {
    color: #0275d8;
  }

  .formulario form {
    margin-top: 20px;
  }

  .formulario .form-control label {
    color: #727272;
  }

  .formulario .form-control {
    border-color: #ddd;
  }

  .formulario .form-control:focus {
    box-shadow: none;
    border-color: #0275d8;
  }

  .formulario {
    max-width: 450px;
    margin: 0 auto;
    font-size: 16px;
    line-height: 1.5;
    margin-top: 30px;
  }

  .formulario p {
    margin-bottom: 0.5rem;
  }

  .formulario a {
    color: #007bff;
  }

  .card {
    margin-top: 30px;
    max-width: 500px;
  }
</style>
