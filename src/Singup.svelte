<script>
  import { Link } from "svelte-routing";
  import { getContext } from "svelte";

  const URL = getContext("URL");

  let mostrarSpinner = false;

  async function handleFormSubmit(event) {
    event.preventDefault(); // Evitar que se envíe el formulario

    const form = event.target; // Obtener el formulario actual

    mostrarSpinner = true;

    // Obtener los valores de los campos de entrada
    const nombre = form.nombre.value;
    const apellidos = form.apellidos.value;
    const correo = form.correo.value;
    const password = form.password.value;
    const rol = form.rol.value;

    try {
      const response = await fetch(URL.singup, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          nombre,
          apellidos,
          correo,
          password,
          rol,
        }),
      });

      const data = await response.json();

      if (response.ok) {
        console.log("Usuario creado con éxito");
        window.location.href = "/login";
      } else if (response.status === 409) {
        document.getElementById("error").innerHTML =
          "Ya existe un usuario con ese correo, introduzca otro";
        console.log("Ya existe un usuario con ese correo");
      } else {
        document.getElementById("error").innerHTML =
          "Rellena y revise todos los campos";
        console.log("No se ha registrado al usuario");
      }
    } catch (error) {
      console.error(error);
    } finally {
      mostrarSpinner = false;
    }
  }
</script>

<div class="container my-5">
  <Link to="/" class="boton my-4">⬅ Volver</Link>
</div>
<div class="container formulario">
  <p class="fs-4 text-center">CREAR CUENTA</p>
  {#if mostrarSpinner}
    <div class="d-flex justify-content-center">
      <div class="spinner-grow text-success" role="status" />
    </div>
  {/if}
  <p id="error" style="color: red; font-weight: bold; text-align: center;" />
  <form on:submit={handleFormSubmit}>
    <input
      type="text"
      class="form-control"
      id="registroNombre"
      name="nombre"
      placeholder="Introduzca su nombre"
    /><br />
    <input
      type="text"
      class="form-control"
      id="registroApellido"
      name="apellidos"
      placeholder="Introduzca sus apellidos"
    /><br />
    <input
      type="email"
      class="form-control"
      id="registroEmail"
      name="correo"
      placeholder="nombre@ejemplo.com"
    /><br />
    <input
      type="password"
      class="form-control"
      id="registroPassword"
      name="password"
      placeholder="Contraseña"
    /><br />

    <p class="text-start">Tipo de cuenta:</p>
    <div class="btn-group d-flex mb-3" role="group">
      <input
        type="radio"
        class="btn-check"
        name="rol"
        id="rol1"
        checked="checked"
        value="profesor"
        autocomplete="off"
      />
      <label class="btn btn-outline-primary" for="rol1">Profesor</label>

      <input
        type="radio"
        class="btn-check"
        name="rol"
        id="rol2"
        value="alumno"
        autocomplete="off"
      />
      <label class="btn btn-outline-primary" for="rol2">Alumno</label>
    </div>
    <div class="d-grid justify-content-center">
      <Link to="/login">¿Ya tienes una cuenta? Inicia sesión</Link><br />
      <input
        id="submitRegistro"
        name="registra"
        class="boton"
        type="submit"
        value="Registrarse"
      />
    </div>
  </form>
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
  }

  .formulario p {
    margin-bottom: 0.5rem;
  }

  .formulario a {
    color: #007bff;
  }
</style>
