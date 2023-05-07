<script>
  import { Link } from "svelte-routing";
  import { getContext } from "svelte";

  const URL = getContext("URL");

  let loginSuccess = false;
  let loginMessage = "";

  /* async function login(correo, password) {
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

    const data = await response.json();
    loginSuccess = data.success;
    loginMessage = data.message;
  } */

  /* async function login(correo, password) {
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

    const data = await response.json();
    loginSuccess = data.success;
    loginMessage = data.message;

    if (data.success) {
      const user = {
        id_usuario: data.id_usuario,
        nombre: data.nombre,
        apellidos: data.apellidos,
        correo: data.correo,
        rol: data.rol,
      };
      localStorage.setItem("user", JSON.stringify(user));
      window.location.href = "/";
    }
  } */

  //ESTO ES UNA VEZ SEA CAPAZ DE ACTUALIZAR EL ID DEL ADMINISTRADOR
  /* async function login(correo, password) {
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

    const data = await response.json();
    loginSuccess = data.success;
    loginMessage = data.message;

    if (data.success) {
      const user = {
        id_usuario: data.id_usuario,
        nombre: data.nombre,
        apellidos: data.apellidos,
        correo: data.correo,
        rol: data.rol,
      };

      try {
        const response = await fetch(URL.autoescuela + "usuario/" + user.id_usuario, {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
        });

        const data = await response.json();
        const id_autoescuela = data.id_autoescuela;
        user.id_autoescuela = id_autoescuela;
        localStorage.setItem("user", JSON.stringify(user));
        window.location.href = "/";
      } catch (error) {
        console.error(error);
      }
    }
  } */

  async function login(correo, password) {
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

    const data = await response.json();
    loginSuccess = data.success;
    loginMessage = data.message;

    if (data.success) {
      const user = {
        id_usuario: data.id_usuario,
        nombre: data.nombre,
        apellidos: data.apellidos,
        correo: data.correo,
        rol: data.rol,
      };

      try {
        if (user.rol === "profesor") {
          // Consulta a la tabla 'profesores'
          const profesorResponse = await fetch(URL.profesor + user.id_usuario, {
            method: "GET",
            headers: {
              "Content-Type": "application/json",
            },
          });
          const profesorData = await profesorResponse.json();

          if (profesorData.id_profesor) {
            // Consulta a la tabla 'profesores_detalle'
            const profesorDetailResponse = await fetch(
              URL.profesor + profesorData.id_profesor,
              {
                method: "GET",
                headers: {
                  "Content-Type": "application/json",
                },
              }
            );
            const profesorDetailData = await profesorDetailResponse.json();
            const id_autoescuela = profesorDetailData.id_autoescuela;

            user.id_autoescuela = id_autoescuela;
          }
        } else if (user.rol === "alumno") {
          // Consulta a la tabla 'alumnos'
          const alumnoResponse = await fetch(URL.alumno + user.id_usuario, {
            method: "GET",
            headers: {
              "Content-Type": "application/json",
            },
          });
          const alumnoData = await alumnoResponse.json();

          if (alumnoData.id_alumno) {
            // Consulta a la tabla 'autoescuelas_alumnos'
            const autoescuelaAlumnoResponse = await fetch(
              URL.alumno + alumnoData.id_alumno,
              {
                method: "GET",
                headers: {
                  "Content-Type": "application/json",
                },
              }
            );
            const autoescuelaAlumnoData =
              await autoescuelaAlumnoResponse.json();
            const id_autoescuela = autoescuelaAlumnoData.id_autoescuela;

            user.id_autoescuela = id_autoescuela;
          }
        }

        localStorage.setItem("user", JSON.stringify(user));
        window.location.href = "/";
      } catch (error) {
        console.error(error);
      }
    }
  }

  async function handleFormSubmit(event) {
    event.preventDefault(); // Evitar que se envíe el formulario

    const form = event.target; // Obtener el formulario actual

    // Obtener los valores de los campos de entrada
    const correo = form.correo.value;
    const password = form.password.value;

    try {
      // Llamar a la función login con el correo y la contraseña
      await login(correo, password);
    } catch (error) {
      console.error(error);
    }
  }
</script>

<div class="container my-5">
  <Link to="/" class="boton my-4">⬅ Volver</Link>
</div>
<div class="container">
  {#if loginSuccess}
    <div class="alert alert-success alert-dismissible fade show" role="alert">
      {loginMessage}<button
        type="button"
        class="btn-close"
        data-bs-dismiss="alert"
        aria-label="Close"
      />
    </div>
  {:else if loginMessage !== ""}
    <div class="alert alert-danger alert-dismissible fade show" role="alert">
      {loginMessage}<button
        type="button"
        class="btn-close"
        data-bs-dismiss="alert"
        aria-label="Close"
      />
    </div>
  {/if}
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
