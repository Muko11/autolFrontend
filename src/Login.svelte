<script>
  import { Link } from "svelte-routing";
  import { getContext } from "svelte";

  const URL = getContext("URL");

  async function handleFormSubmit(event) {
    event.preventDefault(); // Evitar que se envíe el formulario

    const form = event.target; // Obtener el formulario actual

    // Obtener los valores de los campos de entrada
    const correo = form.correo.value;
    const password = form.password.value;

    try {
      // Realizar una solicitud POST a la URL deseada con los datos del formulario
      const response = await fetch(URL.login, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          correo,
          password,
        }),
      });

      // Manejar la respuesta de la solicitud
      const data = await response.json();
      console.log(data);
    } catch (error) {
      console.error(error);
    }
  }
</script>

<div class="container my-5">
  <Link to="/" class="boton my-4">⬅ Volver</Link>
</div>

<div class="container formulario">
  <p class="fs-4 text-center">INICIAR SESIÓN</p>
  <form on:submit={handleFormSubmit}>
    <input
      type="email"
      class="form-control"
      id="loginCorreo"
      name="correo"
      placeholder="Introduzca su correo electrónico"
    /><br />
    <input
      type="password"
      class="form-control"
      id="loginPassword"
      name="password"
      placeholder="Introduzca su contraseña"
    /><br />

    <div class="d-grid justify-content-center">
      <Link to="/singup">¿No tienes cuenta? Crear una cuenta</Link><br />
      <input
        id="submitLogin"
        class="boton"
        type="submit"
        name="login"
        value="Iniciar sesión"
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
