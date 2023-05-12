<script>
  import { Link } from "svelte-routing";

  const user = JSON.parse(localStorage.getItem("user"));
  const nombre = user.nombre;
  const apellidos = user.apellidos;
  const rol = user.rol;
  const correo = user.correo;
  const id_autoescuela = user.id_autoescuela;
</script>

<br />
<br />
<br />
<div class="container formulario my-5">
  <p class="fs-3">Bienvenid@, {nombre}</p>
  <p class="text-muted">Datos de tu cuenta</p>

  <div class="card">
    <div class="card-body">
      <h5 class="card-title"><b>{nombre} {apellidos}</b></h5>
      <p class="card-text"><b>Tipo de cuenta:</b> {rol.toUpperCase()}</p>
      <p class="card-text"><b>Correo electrónico:</b> {correo}</p>
    </div>
  </div>

  {#if id_autoescuela && rol === "profesor"}
    <div class="mt-4 d-flex justify-content-end">
      <Link to="/profesor" class="boton">Ir a mi autoescuela</Link>
    </div>
  {:else if id_autoescuela && rol === "alumno"}
    <div class="mt-4 d-flex justify-content-end">
      <Link to="/alumno" class="boton">Ir a mi autoescuela</Link>
    </div>
  {:else}
    <div class="mt-4 d-flex justify-content-end">
      <Link to="/" class="boton">⬅ Volver</Link>
    </div>
  {/if}
</div>

<style>
  body {
    background-color: #f5f5f5;
  }

  .container {
    max-width: 600px;
    margin: 0 auto;
  }

  .formulario {
    padding: 30px;
    background-color: #f5f5f5;
  }

  .card {
    border: none;
    margin-top: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  }

  .card-body {
    padding: 20px;
  }

  .card-title {
    margin-bottom: 10px;
  }

  .card-text {
    margin-bottom: 5px;
  }

  b {
    font-weight: 600;
  }
</style>
