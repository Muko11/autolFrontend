<script>
  import { onMount } from "svelte";
  import { getContext } from "svelte";
  import { format } from "date-fns";

  const URL = getContext("URL");
  const user = JSON.parse(localStorage.getItem("user"));
  const id_usuario = user.id_usuario;
  const id_autoescuela = user.id_autoescuela;

  let nombre = "";
  let telefono = "";
  let practica = "";
  let practicas = [];
  let historial = [];
  let comunicados = [];
  let filtrarPracticas = "";
  let historialIdSeleccionado;
  let modalHistorial;

  async function datosAutoescuela() {
    const response = await fetch(URL.autoescuela + id_autoescuela);
    const autoescuela = await response.json();
    nombre = autoescuela.nombre;
    telefono = autoescuela.telefono;
    practica = autoescuela.precio_practica;
  }

  onMount(async () => {
    await datosAutoescuela();
  });

  /* Mostrar practicas */
  async function obtenerPracticas() {
    const response = await fetch(URL.practica + "alumnos/" + id_autoescuela);
    const data = await response.json();
    practicas = Object.values(data);
    console.log(practicas);
  }

  /* Actualizar null de id_alumno */

  const actualizarPractica = async (id_profesor, fecha, hora, id_alumno) => {
    try {
      const response = await fetch(
        URL.practica +
          "alumno/" +
          id_profesor +
          "/" +
          fecha +
          "/" +
          hora +
          "/" +
          id_alumno,
        {
          method: "PUT",
        }
      );
      const data = await response.json();
      console.log(data.message);
      if (response.ok) {
        obtenerPracticas();
        obtenerHistorial();

        // Mostrar el toast
        const toast = document.querySelector("#toastReservarPractica");
        toast.classList.add("show");

        // Ocultar el toast después de 7 segundos
        setTimeout(() => {
          toast.classList.remove("show");
        }, 7000);
      }
    } catch (error) {
      console.log(error);

      // Mostrar el toast
      const toast = document.querySelector("#toastErrorReservarPractica");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    }
  };

  onMount(obtenerPracticas);

  /* Mostrar historial */
  async function obtenerHistorial() {
    try {
      const response = await fetch(URL.practica + "historial/" + id_usuario);
      const data = await response.json();
      historial = Object.values(data);
      console.log(historial);
    } catch (error) {
      console.log(error);
    }
  }

  onMount(async () => {
    await obtenerHistorial();
  });

  /* Cancelar practica */
  async function cancelarPractica(id_profesor, fecha, hora) {
    try {
      const response = await fetch(
        URL.practica +
          "cancelar/" +
          id_profesor +
          "/" +
          fecha +
          "/" +
          hora +
          "/" +
          id_usuario,
        {
          method: "PUT",
        }
      );
      const data = await response.json();
      console.log(data);
      obtenerHistorial();
      obtenerPracticas();
      modalHistorial.hide();

      // Mostrar el toast
      const toast = document.querySelector("#toastCancelarPractica");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    } catch (error) {
      console.log(error);

      // Mostrar el toast
      const toast = document.querySelector("#toastErrorCancelarPractica");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    }
  }

  /* Calcular precio practicas */
  async function calcularPrecio(event) {
    event.preventDefault();
    const practicaCompletada =
      +document.getElementById("practicaCompletada").value;
    const precioPorClase = +document.getElementById(
      "practicaAutoescuelaHistorial"
    ).value;
    const totalPracticasInput = document.getElementById("totalPracticas");

    const total = practicaCompletada * precioPorClase;

    totalPracticasInput.value = `${total}€`;
  }

  /* Mostrar comunicados */
  async function obtenerComunicados() {
    const response = await fetch(URL.comunicado + "alumnos/" + id_autoescuela);
    const data = await response.json();
    comunicados = Object.values(data);
    console.log(comunicados);
  }

  onMount(obtenerComunicados);


  onMount(() => {
    // Inicializar la modal cuando el componente se monta
    modalHistorial = new bootstrap.Modal(
      document.getElementById("modalBorrarRegistroHistorial"),
      {
        backdrop: "static", // Evitar que se cierre al hacer clic fuera de la modal
        keyboard: false, // Evitar que se cierre al presionar la tecla Escape
      }
    );
  });
</script>

<!-- Nav lateral -->
<div class="container-fluid panelContenido">
  <div class="row my-3">
    <div class="col mt-5 mb-3">
      <div>
        <h3 class="text-uppercase text-center">Bienvenido a tu autoescuela</h3>

        <div class="toast-container position-static">
          <!-- Toast Reservar Práctica -->

          <div
            class="toast bg-success"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastReservarPractica"
          >
            <div class="toast-header">
              <img
                src="imagenes/logo.svg"
                style="width: 30px;"
                class="rounded me-2"
                alt="Logo"
              />
              <strong class="me-auto">¡Operación exitosa!</strong>
              <small class="text-muted">AutoL</small>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="toast"
                aria-label="Close"
              />
            </div>
            <div class="toast-body text-white">
              Has reservado una práctica con éxito. Puedes verla en tu historial
            </div>
          </div>

          <!-- Toast Error Reservar Práctica -->

          <div
            class="toast bg-danger"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastErrorReservarPractica"
          >
            <div class="toast-header">
              <img
                src="imagenes/logo.svg"
                style="width: 30px;"
                class="rounded me-2"
                alt="Logo"
              />
              <strong class="me-auto">¡Operación fallida!</strong>
              <small class="text-muted">AutoL</small>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="toast"
                aria-label="Close"
              />
            </div>
            <div class="toast-body text-white">
              No has podido reservar la práctica. Porfavor intentalo de nuevo o
              recarga la página
            </div>
          </div>

          <!-- Toast Cancelar Práctica -->

          <div
            class="toast bg-success"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastCancelarPractica"
          >
            <div class="toast-header">
              <img
                src="imagenes/logo.svg"
                style="width: 30px;"
                class="rounded me-2"
                alt="Logo"
              />
              <strong class="me-auto">¡Operación exitosa!</strong>
              <small class="text-muted">AutoL</small>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="toast"
                aria-label="Close"
              />
            </div>
            <div class="toast-body text-white">
              Práctica cancelada con éxito
            </div>
          </div>

          <!-- Toast Error Cancelar Práctica -->

          <div
            class="toast bg-danger"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastErrorCancelarPractica"
          >
            <div class="toast-header">
              <img
                src="imagenes/logo.svg"
                style="width: 30px;"
                class="rounded me-2"
                alt="Logo"
              />
              <strong class="me-auto">¡Operación fallida!</strong>
              <small class="text-muted">AutoL</small>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="toast"
                aria-label="Close"
              />
            </div>
            <div class="toast-body text-white">
              Error al cancelar la práctica. Inténtalo de nuevo o recarga la
              página
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="row g-2">
    <div class="col-md-3 mb-4">
      <div class="aside">
        <nav>
          <h3 class="text-center pb-3">Menú</h3>
          <div
            class="nav nav-tabs d-grid border-bottom-0"
            id="nav-tab"
            role="tablist"
          >
            <a
              href="#"
              class="nav-item nav-link active"
              id="tab-autoescuela"
              data-bs-toggle="tab"
              data-bs-target="#nav-autoescuela"
              role="tab"
              aria-selected="true">Mi autoescuela</a
            >

            <a
              href="#"
              class="nav-item nav-link"
              id="tab-comunicado"
              data-bs-toggle="tab"
              data-bs-target="#nav-comunicado"
              role="tab"
              aria-selected="false">Comunicados</a
            >

            <a
              href="#"
              class="nav-item nav-link"
              id="tab-practica"
              data-bs-toggle="tab"
              data-bs-target="#nav-practica"
              role="tab"
              aria-selected="false">Prácticas</a
            >

            <a
              href="#"
              class="nav-item nav-link"
              id="tab-historial"
              data-bs-toggle="tab"
              data-bs-target="#nav-historial"
              role="tab"
              aria-selected="false">Historial</a
            >
          </div>
        </nav>
      </div>
    </div>
    <div class="col-md-9">
      <div class="row g-4">
        <div class="col">
          <div class="tab-content" id="nav-tabContent">
            <!-- Aside - Mi autoescuela -->

            <div class="tab-pane fade show active px-4" id="nav-autoescuela">
              <h4 class="mb-4">Datos de la autoescuela</h4>
              <form>
                <div class="row row-cols-1 row-cols-lg-3">
                  <div class="mb-4">
                    <label for="nombreAutoescuela" class="fw-bold mb-2"
                      >Nombre de la autoescuela</label
                    >
                    <input
                      type="text"
                      class="form-control"
                      id="nombreAutoescuela"
                      name="nombre"
                      value={nombre}
                      disabled
                    />
                  </div>
                  <div class="mb-4">
                    <label for="telefonoAutoescuela" class="fw-bold mb-2"
                      >Teléfono</label
                    >
                    <input
                      type="tel"
                      class="form-control"
                      id="telefonoAutoescuela"
                      name="telefono"
                      value={telefono}
                      disabled
                    />
                  </div>
                  <div class="mb-4">
                    <label for="practicaAutoescuela" class="fw-bold mb-2"
                      >Precio por clase práctica</label
                    >
                    <input
                      type="text"
                      class="form-control"
                      id="practicaAutoescuela"
                      name="practica"
                      step=".01"
                      value={practica + "€"}
                      disabled
                    />
                  </div>
                </div>
              </form>
            </div>

            <!-- Aside - Comunicados -->

            <div class="tab-pane fade show px-4" id="nav-comunicado">
              <h4 class="mb-4">Comunicados</h4>

              <div class="row row-cols">
                {#each comunicados as comunicado}
                  <div class="comunicados mb-5">
                    <h4><b>{comunicado.titulo}</b></h4>
                    <h6><b>{comunicado.profesor.nombre} {comunicado.profesor.apellidos}</b></h6>
                    <p>
                      <small
                        >{format(
                          new Date(comunicado.fecha),
                          "dd-MM-yyyy"
                        )}</small
                      >
                    </p>
                    <p>{comunicado.mensaje}</p>
                  </div>
                {/each}
              </div>
            </div>

            <!-- Aside - Prácticas -->

            <div class="tab-pane fade show px-4" id="nav-practica">
              <h4 class="mb-4">Reservar práctica</h4>

              <input
                type="text"
                class="form-control my-4"
                id="buscadorPracticas"
                placeholder="Buscar prácticas"
                bind:value={filtrarPracticas}
              />

              <div class="table-responsive-lg">
                <table class="table text-center">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">Fecha</th>
                      <th scope="col">Hora</th>
                      <th scope="col">Tipo</th>
                      <th scope="col">Profesor</th>
                      <th scope="col">Reservar</th>
                    </tr>
                  </thead>
                  <tbody id="tablaPracticas">
                    {#each practicas
                      .sort((a, b) => a.fecha.localeCompare(b.fecha) || a.hora.localeCompare(b.hora))
                      .filter((practica) => {
                        const filtro = filtrarPracticas.toLowerCase();
                        return (practica.tipo
                            .toLowerCase()
                            .includes(filtro) || practica.hora
                              .toLowerCase()
                              .includes(filtro) || practica.fecha
                              .toLowerCase()
                              .includes(filtro) || practica.profesor.nombre
                              .toLowerCase()
                              .includes(filtro) || practica.profesor.apellidos
                              .toLowerCase()
                              .includes(filtro)) && practica.id_alumno === null;
                      }) as practica, i}
                      <tr>
                        <td><b>{i + 1}</b></td>
                        <td>{format(new Date(practica.fecha), "dd-MM-yyyy")}</td
                        >
                        <td>{practica.hora.slice(0, 5)}</td>
                        <td>{practica.tipo.toUpperCase()}</td>
                        <td
                          >{practica.profesor.nombre}
                          {practica.profesor.apellidos}</td
                        >
                        <td>
                          <i
                            class="fa-regular fa-hand fa-2x i-reserva"
                            type="button"
                            on:click={() =>
                              actualizarPractica(
                                practica.id_profesor,
                                practica.fecha,
                                practica.hora,
                                id_usuario
                              )}
                          />
                        </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>

            <!-- Aside - Historial -->

            <div class="tab-pane fade show px-4" id="nav-historial">
              <h4 class="mb-4">Prácticas reservadas</h4>
              <div class="table-responsive-lg mb-5">
                <table class="table text-center">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">Fecha</th>
                      <th scope="col">Hora</th>
                      <th scope="col">Tipo</th>
                      <th scope="col">Profesor</th>
                      <th scope="col">Acciones</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each historial as practica, i}
                      <tr>
                        <td><b>{i + 1}</b></td>
                        <td>{format(new Date(practica.fecha), "dd-MM-yyyy")}</td
                        >
                        <td>{practica.hora.slice(0, 5)}</td>
                        <td>{practica.tipo.toUpperCase()}</td>
                        <td
                          >{practica.profesor.data.nombre}
                          {practica.profesor.data.apellidos}</td
                        >
                        <td>
                          <!-- <i
                            class="fa-solid fa-trash fa-2x i-trash"
                            type="button"
                            on:click={() =>
                              cancelarPractica(
                                practica.id_profesor,
                                practica.fecha,
                                practica.hora
                              )}
                          /> -->

                          <i
                            class="fa-solid fa-trash fa-2x i-trash"
                            type="button"
                            data-bs-toggle="modal"
                            data-bs-target="#modalBorrarRegistroHistorial"
                            on:click={() => (historialIdSeleccionado = practica)}
                          />
                          </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>

              <h4 class="mb-4">Calcular precio de prácticas realizadas</h4>

              <div
                class="alert alert-primary alert-dismissible fade show mb-4"
                role="alert"
              >
                Puedes calcular el precio que tendrías que pagar por las
                prácticas introduciendo un número de prácticas
                <button
                  type="button"
                  class="btn-close"
                  data-bs-dismiss="alert"
                  aria-label="Close"
                />
              </div>

              <form class="mb-5">
                <div class="row row-cols-1 row-cols-md-1 row-cols-lg-3">
                  <div class="mb-4">
                    <label for="practicaCompletada" class="fw-bold mb-2"
                      >Prácticas completadas</label
                    >
                    <input
                      type="number"
                      class="form-control"
                      id="practicaCompletada"
                      placeholder="Introduzca un número"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="practicaAutoescuela" class="fw-bold mb-2"
                      >Precio por clase práctica</label
                    >
                    <input
                      type="number"
                      class="form-control"
                      id="practicaAutoescuelaHistorial"
                      step=".01"
                      value={practica}
                      disabled
                    />
                  </div>
                  <div class="mb-4">
                    <label for="totalPracticas" class="fw-bold mb-2"
                      >Precio total de las prácticas</label
                    >
                    <input
                      type="text"
                      class="form-control"
                      id="totalPracticas"
                      placeholder="0€"
                      disabled
                    />
                  </div>
                  <div>
                    <button class="boton" on:click={calcularPrecio}
                      >Calcular</button
                    >
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Modal borrar practica -->

<div
  class="modal fade"
  id="modalBorrarRegistroHistorial"
  tabindex="-1"
  aria-hidden="true"
>
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content p-3">
      <div class="modal-header border-bottom-0">
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        />
      </div>
      <div class="modal-body text-center">
        <p class="fs-3">Cancelar práctica</p>
        <p class="text-muted">¿Estás seguro de cancelar la práctica? Podrás volver a reservarla en la pestaña "Prácticas"</p>
        <div class="d-flex justify-content-center mb-3 g-2">
          <button
            id="botonBorrarRegistro"
            class="boton-borrar"
            on:click={() =>
              cancelarPractica(
                historialIdSeleccionado.id_profesor,
                historialIdSeleccionado.fecha,
                historialIdSeleccionado.hora
              )}>Cancelar práctica</button
          >
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  body {
    margin-top: 6%;
  }

  .panelContenido {
    margin-bottom: 7%;
  }

  /* Menú lateral */

  .aside {
    padding: 10px;
    border-left: 5px solid #5499c7;
  }

  .nav-tabs .nav-link {
    text-align: center;
    color: black;
  }

  .nav-tabs .nav-link.active {
    color: white;
    background-color: #5499c7;
    border: 0;
  }

  .nav-link:hover {
    transition: none;
  }

  /* Tablas */
  thead {
    background-color: #5499c7;
    color: white;
    height: 50px;
  }

  table th,
  table tr {
    text-align: left;
    vertical-align: middle;
  }

  /* Comunicados */
  .comunicados {
    border-left: 5px solid #5499c7;
  }

  /* Iconos */
  .iconos-tabla {
    width: 30px;
    height: 30px;
  }

  .i-trash {
    color: red;
  }

  .i-finalizado {
    color: green;
  }

  /* .i-finalizado:hover {
    color: lightseagreen;
} */

  .i-edit {
    color: darkorchid;
  }

  /* .i-edit:hover {
    color: peru;
}
 */
  .i-reserva {
    color: limegreen;
  }

  .i-historial {
    color: goldenrod;
  }

  .i-añadir {
    color: limegreen;
  }

  /* Botón borrar */
  .boton-borrar {
    background-color: rgb(245, 56, 56);
    color: white;
    text-decoration: none;
    padding: 12px 36px;
    border: 0;
    box-shadow: 4px 4px 6px rgb(0, 0, 0, 0.4);
  }

  .boton-borrar:hover {
    background-color: red;
    color: white;
  }
</style>
