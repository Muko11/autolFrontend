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
  let filtrarPracticas = "";

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
      }
    } catch (error) {
      console.log(error);
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
    const response = await fetch(URL.practica + "cancelar/" + id_profesor + "/" + fecha + "/" + hora + "/" + id_usuario, {
      method: 'PUT',
    });
    const data = await response.json();
    console.log(data);
    obtenerHistorial();
    obtenerPracticas();
  } catch (error) {
    console.log(error);
  }
}


</script>

<!-- Nav lateral -->
<div class="container-fluid panelContenido">
  <div class="row my-3">
    <div class="col mt-5 mb-3">
      <div>
        <h3 class="text-uppercase text-center">Bienvenido a tu autoescuela</h3>
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
                      value={practica}
                      disabled
                    />
                  </div>
                </div>
              </form>
            </div>

            <!-- Aside - Comunicados -->

            <div class="tab-pane fade show px-4" id="nav-comunicado">
              <h4 class="mb-4">Comunicados</h4>

              <div class="row row-cols-1">
                <div class="comunicados mb-5">
                  <h5>Examen práctico día 30 de enero de 2023</h5>
                  <h6>Mari Carmen</h6>
                  <p><small>20/10/2023</small></p>
                  <p>
                    Para presentaros al examen práctico debéis de realizar el
                    pago en la autoescuela antes del día 25 de enero de 2023.
                    Gracias.
                  </p>
                </div>
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
                        <td>{i + 1}</td>
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
                        <td>{i + 1}</td>
                        <td>{format(new Date(practica.fecha), "dd-MM-yyyy")}</td
                        >
                        <td>{practica.hora.slice(0, 5)}</td>
                        <td>{practica.tipo.toUpperCase()}</td>
                        <td
                          >{practica.profesor.data.nombre}
                          {practica.profesor.data.apellidos}</td
                        >
                        <td>
                          <i
                            class="fa-solid fa-trash fa-2x i-trash"
                            type="button"
                            on:click={() =>
                              cancelarPractica(
                                practica.id_profesor,
                                practica.fecha,
                                practica.hora
                              )}
                          /></td
                        >
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
                      type="text"
                      class="form-control"
                      id="practicaAutoescuelaHistorial"
                      step=".01"
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
                    <input class="boton" type="submit" value="Calcular" />
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
