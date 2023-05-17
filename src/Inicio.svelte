<script>
  import { getContext } from "svelte";

  const URL = getContext("URL");

  let user = JSON.parse(localStorage.getItem("user"));
  let isProfessor = user && user.rol === "profesor";

  async function handleFormSubmit(event) {
    event.preventDefault(); // Evitar que se envíe el formulario

    const form = event.target; // Obtener el formulario actual

    // Obtener los valores de los campos de entrada
    const nombre = form.nombre.value;
    const telefono = form.telefono.value;
    const precio_practica = form.precio_practica.value;

    const user = JSON.parse(localStorage.getItem("user"));
    const id_profesor = user.id_usuario;

    // Verificar si el profesor ya pertenece a una autoescuela
    if (user.id_autoescuela) {
      alert("Ya perteneces a una autoescuela");
      return;
    }

    try {
      const response = await fetch(URL.autoescuela, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          nombre,
          telefono,
          precio_practica,
        }),
      });

      const data = await response.json();
      const id_autoescuela = data.id_autoescuela;

      try {
        const response2 = await fetch(
          URL.profesor + id_profesor + "/" + id_autoescuela,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
          }
        );

        const data2 = await response2.json();

        if (data2.error) {
          alert("Rellena todos los campos para crear la autoescuela");
        } else {
          // Actualizar el objeto user con la id_autoescuela
          user.id_autoescuela = id_autoescuela;
          localStorage.setItem("user", JSON.stringify(user));

          try {
            const response3 = await fetch(
              URL.autoescuela + id_autoescuela + "/" + id_profesor,
              {
                method: "POST",
                headers: {
                  "Content-Type": "application/json",
                },
              }
            );

            const data3 = await response3.json();

            if (data3.error) {
              alert("Error al asignar el profesor como administrador");
            } else {
              // La autoescuela ha sido creada correctamente
              window.location.href = "/profesor";
            }
          } catch (error) {
            console.error(error);
          }
        }
      } catch (error) {
        console.error(error);
      }
    } catch (error) {
      console.error(error);
    }
  }
</script>

<!-- Portada -->
<div class="d-flex justify-content-center align-items-center portada">
  <div class="container position-relative">
    <h1 id="inicio">AutoL - Organiza tu autoescuela</h1>
    <h2>Software para la gestión de autoescuelas. ¡Comienza ahora!</h2>
    <br />
    {#if isProfessor}
      {#if !user.id_autoescuela}
        <a href="#oportunidades" class="boton btn-portada">Comenzar</a>
      {/if}
    {/if}
  </div>
</div>

<div id="servicios" />

<!-- Servicios -->

<br />

<div class="container pt-5 pb-5">
  <h3 class="text-center mt-3 mb-5">NUESTROS SERVICIOS</h3>

  <div class="row bg-light">
    <div class="col-md-6 shadow servicios">
      <h4 class="fw-semibold mb-2">Reserva de prácticas</h4>
      <p>Reserva clases prácticas online desde tu hogar</p>
      <img class="iconos m-auto" src="imagenes/reservas.png" alt="Reservas" />
    </div>

    <div class="col-md-6 shadow servicios">
      <h4 class="fw-semibold mb-2">Comunicados</h4>
      <p>Mantén informado a tus alumnos de los exámenes</p>
      <img
        class="iconos m-auto"
        src="imagenes/comunicados.png"
        alt="Comunicados"
      />
    </div>
  </div>

  <div class="row bg-light">
    <div class="col-md-6 shadow servicios">
      <h4 class="fw-semibold mb-2">Calculadora de pagos</h4>
      <p>Calcula cuanto tendrás que pagar por las prácticas</p>
      <img
        class="iconos m-auto"
        src="imagenes/calculadora.png"
        alt="Control prácticas"
      />
    </div>

    <div class="col-md-6 shadow servicios">
      <h4 class="fw-semibold mb-2">Sin límite de alumnos</h4>
      <p>Cada alumno tendrá un hueco asegurado en tu autoescuela</p>
      <img class="iconos m-auto" src="imagenes/alumnos.png" alt="Reservas" />
    </div>
  </div>
</div>

<!-- Tarjetas profesor, alumno, autoescuela -->
{#if isProfessor}
  {#if !user.id_autoescuela}
    <div id="oportunidades" />
    <br />
    <br />

    <div class="container-fluid px-4 py-5 contenedor-autoescuela">
      <h3 class="text-center m-3">CREA TU AUTOESCUELA</h3>
      <div class="container-fluid px-md-5 px-sm-2 mt-4">
        <div class="row row-cols row-cols-md-2 align-items-center">
          <div class="d-flex justify-content-center mb-4">
            <img
              class="w-50"
              src="imagenes/autoescuela.png"
              alt="Autoescuela"
            />
          </div>

          <form
            id="formulario-autoescuela"
            class="p-4"
            on:submit={handleFormSubmit}
          >
            <h4 class="fw-bold mb-4 text-center">
              Rellena el formulario para crear tu autoescuela
            </h4>
            <div class="form-floating mb-3">
              <input
                type="text"
                class="form-control"
                id="modalNombreAutoescuela"
                name="nombre"
                placeholder="Nombre de la autoescuela"
              />
              <label for="modalNombreAutoescuela"
                >Nombre de la autoescuela</label
              >
            </div>
            <div class="form-floating mb-3">
              <input
                type="number"
                class="form-control"
                id="modalTelefono"
                name="telefono"
                placeholder="Teléfono"
              />
              <label for="modalTelefono">Teléfono</label>
            </div>
            <div class="form-floating mb-3">
              <input
                type="number"
                class="form-control"
                id="modalPrecio"
                name="precio_practica"
                placeholder="Precio por clase práctica"
              />
              <label for="modalPrecio">Precio por clase práctica</label>
            </div>
            <div class="d-flex justify-content-center mb-3">
              <input
                id="submitAutoescuela"
                class="boton"
                type="submit"
                value="Crear autoescuela"
              />
            </div>
          </form>
        </div>
      </div>
    </div>
  {/if}
{/if}
<!-- Contacto -->
<div id="contacto" />
<br />
<br />
<br />

<div class="container mb-5">
  <h3 class="text-center m-5 titulos">CONTÁCTANOS</h3>
  <form id="f-contact" class="formulario-contacto p-3">
    <div class="row">
      <div class="col-md-6">
        <div class="mb-4 form-floating">
          <input
            type="text"
            class="form-control"
            id="contactoNombre"
            placeholder="Nombre"
          />
          <label for="contactoNombre">Tu nombre</label>
        </div>
      </div>
      <div class="col-md-6">
        <div class="mb-4 form-floating">
          <input
            type="email"
            class="form-control"
            id="contactoEmail"
            placeholder="Correo electrónico"
          />
          <label for="contactoEmail">Tu correo electrónico</label>
        </div>
      </div>
    </div>
    <div class="mb-4 form-floating">
      <input
        type="text"
        class="form-control"
        id="contactoAsunto"
        placeholder="Asunto"
      />
      <label for="contactoAsunto">Asunto</label>
    </div>

    <div class="mb-4">
      <textarea
        class="form-control"
        id="contactoMensaje"
        rows="5"
        placeholder="Escribe tu mensaje..."
      />
    </div>

    <div class="d-flex justify-content-center mb-4">
      <input class="boton" id="contactoSubmit" type="submit" value="Enviar" />
    </div>
  </form>
</div>
<br />

<style>
  /* Portada */

  .portada {
    width: 100%;
    height: 100vh;
    background: url("../imagenes/portada.jpg") top center;
    background-size: cover;
    position: relative;
  }

  .portada:before {
    content: "";
    background: rgba(0, 0, 0, 0.4);
    position: absolute;
    bottom: 0;
    top: 0;
    left: 0;
    right: 0;
  }

  .portada .container {
    padding-top: 72px;
  }

  .portada h1 {
    margin: 0;
    font-size: 48px;
    font-weight: bold;
    line-height: 56px;
    color: white;
    font-family: "Poppins", sans-serif;
  }

  .portada h2 {
    color: white;
    margin: 10px 0 0 0;
    font-size: 24px;
  }

  .btn-portada {
    box-shadow: 5px 6px 8px 3px rgb(0, 0, 0, 0.8);
  }

  @media (max-width: 992px) {
    .portada .container {
      padding-top: 62px;
    }
  }

  @media (min-width: 1024px) {
    .portada {
      background-attachment: fixed;
    }
  }

  @media (max-width: 768px) {
    .portada {
      height: 100vh;
    }
  }

  @media (max-width: 610px) {
    .portada h1 {
      font-size: 28px;
      line-height: 36px;
      text-align: center;
    }

    .portada h2 {
      font-size: 18px;
      line-height: 24px;
      text-align: center;
    }

    .portada .container .boton {
      display: flex;
      justify-content: center;
    }
  }

  @media (max-width: 992px) {
    .tarjeta {
      display: grid;
      grid-template-columns: 1fr;
      padding: 2%;
    }
  }

  @media (min-width: 768px) {
    .card img {
      height: 11em;
    }
  }

  /* Servicios */

  .servicios {
    padding: 3%;
    text-align: center;
    align-content: stretch;
    align-items: center;
  }

  @media (max-width: 992px) {
    .servicios {
      display: grid;
      grid-template-columns: 1fr;
      padding: 9%;
    }
  }

  /* Formulario Crear Autoescuela */

  .contenedor-autoescuela {
    background-image: url("../imagenes/fondoFormulario.svg");
    background-size: cover;
    position: relative;
  }

  #formulario-autoescuela {
    box-shadow: 11px 13px 20px 7px rgb(0, 0, 0, 0.8);
    background-color: white;
  }

  /* Formulario Contacto */
  .formulario-contacto {
    padding: 15px;
    border-radius: 3px;
    border-left: 5px solid #5499c7;
  }
</style>
