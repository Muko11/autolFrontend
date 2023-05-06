<script>
  import { onMount } from "svelte";
  import { getContext } from "svelte";

  const URL = getContext("URL");
  const user = JSON.parse(localStorage.getItem("user"));
  const id_usuario = user.id_usuario;
  const id_autoescuela = user.id_autoescuela;

  let nombre = "";
  let telefono = "";
  let practica = "";
  let profesores = [];
  let filtrarProfesores = "";

  onMount(async () => {
    try {
      const response = await fetch(
        URL.autoescuela + "usuario/" + id_autoescuela
      );
      const autoescuela = await response.json();
      nombre = autoescuela.nombre;
      telefono = autoescuela.telefono;
      practica = autoescuela.precio_practica;
    } catch (error) {
      console.error(error);
    }
  });

  /* Actualizar datos autoescuela */
  async function handleSubmit(event) {
    event.preventDefault();

    const form = event.target;
    const { nombreAutoescuela, telefonoAutoescuela, practicaAutoescuela } =
      form.elements;

    const data = {
      nombre: nombreAutoescuela.value,
      telefono: telefonoAutoescuela.value,
      precio_practica: practicaAutoescuela.value,
    };

    const response = await fetch(URL.autoescuela + id_usuario, {
      method: "PUT",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(data),
    });

    if (response.ok) {
      // Actualizar los campos del formulario con los nuevos datos
      const newData = await response.json();
      nombreAutoescuela.value = newData.nombre;
      telefonoAutoescuela.value = newData.telefono;
      practicaAutoescuela.value = newData.precio_practica;

      // Mostrar el toast
      const toast = document.querySelector("#toastDatosAutoescuela");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    } else {
      // Manejar errores
      console.error("Ha ocurrido un error al actualizar la autoescuela");
    }
  }

  /* Añadir profesor a autoescuela */

  async function agregarProfesor(event) {
    event.preventDefault();

    const correo = event.target.emailProfesor.value;

    const response = await fetch(
      URL.profesor + "agregar/" + `${correo}` + "/" + id_autoescuela,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ correo }),
      }
    );

    const data = await response.json();

    // Si la respuesta fue exitosa, agregar el id_autoescuela del profesor al localStorage
    if (response.ok) {
      const id_autoescuela_profesor = data.id_autoescuela;
      localStorage.setItem("id_autoescuela", id_autoescuela_profesor);
      obtenerProfesores();

      // Mostrar el toast
      const toast = document.querySelector("#toastAgregarProfesor");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    } else {
      // Mostrar el toast
      const toast = document.querySelector("#toastErrorAgregarProfesor");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    }

    console.log(data);
  }

  /* Mostrar profesores */
  async function obtenerProfesores() {
    const response = await fetch(
      URL.profesor + "autoescuela/" + id_autoescuela
    );
    const data = await response.json();
    profesores = Object.values(data);
    console.log(profesores);
  }

  onMount(obtenerProfesores);

  /* Borrar un profesor */
  async function borrarProfesor(id_profesor) {
    const response = await fetch(URL.profesor + id_profesor, {
      method: "DELETE",
    });

    if (response.ok) {
      // Si se eliminó el profesor correctamente, volvemos a cargar la lista
      obtenerProfesores();

      // Mostrar el toast
      const toast = document.querySelector("#toastBorrarProfesor");
      toast.classList.add("show");

      // Ocultar el toast después de 7 segundos
      setTimeout(() => {
        toast.classList.remove("show");
      }, 7000);
    } else {
      console.error("Error al eliminar el profesor");
    }
  }

  /* Comprobar si el usuario es administrador */

  let es_administrador = false;

  onMount(async () => {
    const response = await fetch(URL.profesor + "admin/" + id_usuario);
    const data = await response.json();
    es_administrador = data.es_administrador;
  });

  /*   let es_administrador = false;

  onMount(async () => {
    const res = await fetch(
      URL.profesor + id_autoescuela + "/" + id_profesor + "/administrador"
    );
    const { es_administrador: esAdmin } = await res.json();
    es_administrador = esAdmin;
  }); */
</script>

<!-- Nav lateral -->
<div class="container-fluid panelContenido">
  <div class="row my-3">
    <div class="col mt-5 mb-3">
      <div>
        <h3 class="text-uppercase text-center">Panel de control</h3>

        <div class="toast-container position-static">
          <!-- Toast Datos Autoescuela -->

          <div
            class="toast bg-success"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastDatosAutoescuela"
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
              Se han actualizado con éxito los datos de la autoescuela
            </div>
          </div>

          <!-- Toast Agregar profesor -->

          <div
            class="toast bg-success"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastAgregarProfesor"
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
              Se ha añadido al profesor a la autoescuela correctamente
            </div>
          </div>

          <!-- Toast Error Agregar profesor -->

          <div
            class="toast bg-danger"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastErrorAgregarProfesor"
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
              No se ha podido añadir al profesor porque ya está asignado a una
              autoescuela, no es un profesor o no existe el correo
            </div>
          </div>

          <!-- Toast Borrar profesor -->

          <div
            class="toast bg-success"
            role="alert"
            aria-live="assertive"
            aria-atomic="true"
            id="toastBorrarProfesor"
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
              Se ha borrado con éxito al profesor de la autoescuela
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
              id="tab-profesor"
              data-bs-toggle="tab"
              data-bs-target="#nav-profesor"
              role="tab"
              aria-selected="false">Profesores</a
            >

            <a
              href="#"
              class="nav-item nav-link"
              id="tab-alumno"
              data-bs-toggle="tab"
              data-bs-target="#nav-alumno"
              role="tab"
              aria-selected="false">Alumnos</a
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
              id="tab-comunicado"
              data-bs-toggle="tab"
              data-bs-target="#nav-comunicado"
              role="tab"
              aria-selected="false">Comunicados</a
            >
          </div>
        </nav>
      </div>
    </div>
    <div class="col-md-9">
      <div class="row g-4">
        <div class="col">
          <div class="tab-content" id="nav-tabContent">
            <!-- Aside - Código autoescuela -->

            <div class="tab-pane fade show active px-4" id="nav-autoescuela">
              <div
                class="row row-cols-sm-2 row-cols-md-2 d-flex justify-content-between mb-4"
              >
                <h4>Datos de la autoescuela</h4>
              </div>

              <form class="mb-5" on:submit={handleSubmit}>
                <div class="row row-cols-1 row-cols-md-1 row-cols-lg-3">
                  <div class="mb-4">
                    <label for="nombreAutoescuela" class="fw-bold mb-2"
                      >Nombre de la autoescuela</label
                    >
                    <input
                      type="text"
                      class="form-control"
                      id="nombreAutoescuela"
                      name="nombre"
                      placeholder="Nombre de la autoescuela"
                      bind:value={nombre}
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
                      placeholder="Teléfono"
                      bind:value={telefono}
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
                      placeholder="Precio por clase práctica"
                      bind:value={practica}
                    />
                  </div>
                </div>
                {#if es_administrador}
                  <div>
                    <input
                      class="boton"
                      type="submit"
                      name="actualizaDatos"
                      value="Guardar cambios"
                    />
                  </div>
                {/if}
                <br />
                <!-- <div class="d-flex justify-content-end">
                  <a
                    href="#"
                    class="boton-borrar"
                    data-bs-toggle="modal"
                    data-bs-target="#modalBorrarAutoescuela"
                    >Borrar autoescuela</a
                  >
                </div> -->
              </form>
            </div>

            <!-- Aside - Profesor -->

            <div class="tab-pane fade show px-4" id="nav-profesor">
              <h4 class="mb-4">Profesores</h4>

              <!-- <a href="#" data-bs-toggle="modal" data-bs-target="#modalAñadirProfesor"><i class="fa-solid fa-user-plus fa-2x i-añadir"></i></a> -->
              {#if es_administrador}
                <form class="mb-5" on:submit={agregarProfesor}>
                  <div class="row row-cols row-cols-md-2">
                    <div class="mb-4">
                      <label for="emailProfesor" class="fw-bold mb-2"
                        >Agregar profesor</label
                      >
                      <input
                        type="email"
                        class="form-control"
                        id="emailProfesor"
                        name="emailProfesor"
                        placeholder="Correo del profesor"
                      />
                    </div>
                  </div>
                  <div>
                    <input
                      class="boton"
                      type="submit"
                      name="botonAñadirProfesor"
                      value="Agregar profesor"
                    />
                  </div>
                </form>
              {/if}
              <input
                type="text"
                class="form-control my-4"
                id="buscadorProfesor"
                placeholder="Buscar profesores"
                bind:value={filtrarProfesores}
              />

              <div class="table-responsive-lg">
                <table class="table text-center">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">Profesor</th>
                      <th scope="col">Email</th>
                      {#if es_administrador}
                        <th scope="col">Acciones</th>
                      {/if}
                    </tr>
                  </thead>
                  <tbody id="tablaProfesores">
                    {#each profesores
                      .sort((a, b) => a.nombre.localeCompare(b.nombre) || a.apellidos.localeCompare(b.apellidos))
                      .filter((profesor) => profesor.nombre
                            .toLowerCase()
                            .includes(filtrarProfesores.toLowerCase()) || profesor.apellidos
                            .toLowerCase()
                            .includes(filtrarProfesores.toLowerCase()) || profesor.correo
                            .toLowerCase()
                            .includes(filtrarProfesores.toLowerCase())) as profesor, i}
                      <tr>
                        <td><b>{i + 1}</b></td>
                        <td>{profesor.nombre} {profesor.apellidos}</td>
                        <td>{profesor.correo}</td>
                        {#if es_administrador}
                          <td>
                            {#if profesor.id_profesor === id_usuario}
                              <div style="display: flex; align-items: center">
                                <i class="fa-solid fa-star fa-2x i-start" />
                                <b class="ms-1">Administrador</b>
                              </div>
                            {:else}
                              <i
                                class="fa-solid fa-trash fa-2x i-trash"
                                type="button"
                                on:click={() =>
                                  borrarProfesor(profesor.id_profesor)}
                              />
                            {/if}
                          </td>
                        {/if}
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>

            <!-- Aside - Alumno -->

            <div class="tab-pane fade show px-4" id="nav-alumno">
              <h4 class="mb-4">Alumnos</h4>

              <!-- <a href="#" data-bs-toggle="modal" data-bs-target="#modalAñadirAlumno"><i class="fa-solid fa-user-plus fa-2x i-añadir"></i></a> -->

              <form
                class="mb-5"
                method="POST"
                action="forms/procesarAlumno.php"
              >
                <div class="row row-cols row-cols-md-2">
                  <div class="mb-4">
                    <label for="emailAlumno" class="fw-bold mb-2"
                      >Agregar alumno</label
                    >
                    <input
                      type="email"
                      class="form-control"
                      id="emailAlumno"
                      name="emailAlumno"
                      placeholder="Correo del alumno"
                    />
                  </div>
                </div>
                <div>
                  <input
                    class="boton"
                    type="submit"
                    name="botonAñadirAlumno"
                    value="Agregar alumno"
                  />
                </div>
              </form>

              <form>
                <div class="my-4">
                  <input
                    type="text"
                    class="form-control"
                    id="buscadorAlumno"
                    placeholder="Buscar alumnos"
                    value=""
                  />
                </div>
              </form>

              <div class="table-responsive-lg">
                <table class="table text-center">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">Alumno</th>
                      <th scope="col">Email</th>
                      <th scope="col">Acciones</th>
                    </tr>
                  </thead>
                  <tbody id="tablaAlumnos" />
                </table>
              </div>
            </div>

            <!-- Aside - Prácticas -->

            <div class="tab-pane fade show px-4" id="nav-practica">
              <h4 class="mb-4">Crear una práctica</h4>

              <form
                class="mb-5"
                method="POST"
                action="forms/procesarPractica.php"
              >
                <div class="row row-cols-1 row-cols-md-1 row-cols-lg-3">
                  <div class="mb-4">
                    <label for="fechaPractica" class="fw-bold mb-2">Fecha</label
                    >
                    <input
                      type="date"
                      class="form-control"
                      id="fechaPractica"
                      name="fecha"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="horaPractica" class="fw-bold mb-2">Hora</label>
                    <input
                      type="time"
                      class="form-control"
                      id="horaPractica"
                      name="hora"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="tipoPractica" class="fw-bold mb-2"
                      >Tipo de práctica</label
                    >
                    <select class="form-control" id="tipoPractica" name="tipo">
                      <option value="">Selecciona un tipo de práctica</option>
                      <option value="am">AM</option>
                      <option value="a1">A1</option>
                      <option value="a2">A2</option>
                      <option value="a">A</option>
                      <option value="b1">B1</option>
                      <option value="b">B</option>
                      <option value="c1">C1</option>
                      <option value="c">C</option>
                      <option value="d1">D1</option>
                      <option value="d">D</option>
                      <option value="be">BE</option>
                      <option value="c1e">C1E</option>
                      <option value="ce">CE</option>
                      <option value="d1e">D1E</option>
                      <option value="de">DE</option>
                    </select>
                  </div>
                  <div>
                    <input
                      class="boton"
                      type="submit"
                      name="crearPractica"
                      value="Crear práctica"
                    />
                  </div>
                </div>
              </form>

              <h4 class="mb-4">Tabla de prácticas</h4>

              <div class="table-responsive-lg">
                <table class="table text-center">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">Fecha</th>
                      <th scope="col">Hora</th>
                      <th scope="col">Tipo</th>
                      <th scope="col">Alumno</th>
                      <th scope="col">Acciones</th>
                    </tr>
                  </thead>
                  <tbody />
                </table>
              </div>
            </div>

            <!-- Aside - Comunicados -->

            <div class="tab-pane fade show px-4" id="nav-comunicado">
              <h4 class="mb-4">Crear un comunicado</h4>

              <form class="mb-5">
                <div class="row">
                  <div class="col">
                    <div class="mb-3">
                      <input
                        type="text"
                        class="form-control"
                        id="comunicadoTitulo"
                        placeholder="Título del comunicado"
                      />
                    </div>
                  </div>
                </div>
                <div class="mb-3">
                  <textarea
                    class="form-control"
                    id="comunicadoMensaje"
                    rows="5"
                    placeholder="Escribe tu comunicado..."
                  />
                </div>

                <div>
                  <input
                    class="boton"
                    id="comunicadoSubmit"
                    type="submit"
                    value="Publicar"
                  />
                </div>
              </form>

              <h4 class="mb-4">Lista de comunicados</h4>

              <div class="row row-cols">
                <div class="comunicados mb-5">
                  <h5>Examen práctico día 30 de enero de 2023</h5>
                  <h6>Mari Carmen</h6>
                  <p><small>20/10/2023</small></p>
                  <p>
                    Para presentaros al examen práctico debéis de realizar el
                    pago en la autoescuela antes del día 25 de enero de 2023.
                    Gracias.
                  </p>
                  <a
                    href="#"
                    data-bs-toggle="modal"
                    data-bs-target="#modalEditarComunicado"
                    ><i class="fa-solid fa-pen-to-square fa-2x i-edit" /></a
                  >

                  <a
                    href="#"
                    data-bs-toggle="modal"
                    data-bs-target="#modalBorrarComunicado"
                    ><i class="fa-solid fa-trash fa-2x i-trash" /></a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  /* Margenes body y contenedor panelContenido */

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

  table tbody tr:hover {
    background-color: aliceblue;
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

  .i-start {
    color: gold;
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
