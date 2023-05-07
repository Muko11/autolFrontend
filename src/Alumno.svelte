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

              <form class="mb-5">
                <div class="row row-cols-1 row-cols-md-1 row-cols-lg-3">
                  <div class="mb-4">
                    <label for="fechaPractica" class="fw-bold mb-2"
                      >Filtrar por fecha</label
                    >
                    <input
                      type="date"
                      class="form-control"
                      id="fechaPractica"
                    />
                  </div>
                  <div class="mb-4">
                    <label for="profesorPractica" class="fw-bold mb-2"
                      >Filtrar por profesor</label
                    >
                    <select id="profesorPractica" class="form-control">
                      <option value="" selected="selected"
                        >Selecciona un profesor</option
                      >
                      <option value="">Oterino</option>
                      <option value="">Rafa</option>
                      <option value="">Ester</option>
                    </select>
                  </div>
                  <div class="mb-4">
                    <label for="tipoPractica" class="fw-bold mb-2"
                      >Filtrar por tipo de práctica</label
                    >
                    <select class="form-control" id="tipoPractica">
                      <option value="">Selecciona un tipo de práctica</option>
                      <option value="">AM</option>
                      <option value="">A1</option>
                      <option value="">A2</option>
                      <option value="">A</option>
                      <option value="">B1</option>
                      <option value="">B</option>
                      <option value="">C1</option>
                      <option value="">C</option>
                      <option value="">D1</option>
                      <option value="">D</option>
                      <option value="">BE</option>
                      <option value="">C1E</option>
                      <option value="">CE</option>
                      <option value="">D1E</option>
                      <option value="">DE</option>
                    </select>
                  </div>
                  <div>
                    <input
                      class="boton"
                      type="submit"
                      value="Buscar prácticas"
                    />
                  </div>
                </div>
              </form>

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
                  <tbody />
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
                      <th scope="col">Cancelar</th>
                    </tr>
                  </thead>
                  <tbody />
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
