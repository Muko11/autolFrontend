<script>
  import { Link } from "svelte-routing";
  import { onMount } from "svelte";

  let isLogged;
  let isProfessor;
  let isAlumno;
  const user = JSON.parse(localStorage.getItem("user"));

  onMount(() => {
    // Verificar si hay un usuario en sesión al cargar la página
    const user = JSON.parse(localStorage.getItem("user"));
    isLogged = !!user;

    if (isLogged) {
      isProfessor = user.rol === "profesor";
    }
  });

  document.addEventListener("DOMContentLoaded", function () {
    const user = JSON.parse(localStorage.getItem("user"));
    const nombreLogin = user.nombre;

    // Obtener el elemento 'a'
    const navbarBrand = document.querySelector(".navbar-brand");

    // Obtener el nombre de usuario de la sesión
    const nombreUsuario = nombreLogin;

    // Actualizar el contenido del elemento 'a'
    navbarBrand.innerHTML = nombreUsuario;

    // Poner un subrayado al elemento
    navbarBrand.style.textDecoration = "underline";
  });
</script>

<div class="container-fluid g-0 mb-5">
  <nav id="nav-scroll" class="navbar navbar-expand-lg fixed-top bg-dark">
    <div class="container-fluid">
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNavDropdown"
        aria-controls="navbarNavDropdown"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <i class="fa-sharp fa-solid fa-bars fa-2x" />
      </button>
      <ul class="navbar-nav mx-4">
        <li class="nav-item toogle-logo">
          <Link to="/" class="nav-link"
            ><img
              class="iconos-menu"
              src="imagenes/logo.svg"
              alt="Logo"
            /></Link
          >
        </li>
      </ul>
      <div
        class="menu menu-grid collapse navbar-collapse mx-4"
        id="navbarNavDropdown"
      >
        <div class="navbar-nav">
          <div class="contenido-menu toogle-contenido">
            <div class="nav-item">
              <Link to="/" class="nav-link">Inicio</Link>
            </div>
            <div class="nav-item">
              <a class="nav-link" href="/#servicios">Servicios</a>
            </div>
            {#if isProfessor}
              {#if !user.id_autoescuela}
                <div class="nav-item">
                  <a class="nav-link" href="/#oportunidades"
                    >Crear autoescuela</a
                  >
                </div>
              {/if}
            {/if}
            <div class="nav-item">
              <a class="nav-link" href="/#contacto">Contacto</a>
            </div>
          </div>
        </div>
        <ul class="navbar-nav dropstart">
          <li class="nav-item dropdown toogle-usuario">
            <a
              class="navbar-brand nav-link dropdown-toggle me-0"
              href="#"
              id="dropdownMenu"
              data-bs-toggle="dropdown"><i class="fa-solid fa-user fa-lg" /></a
            >
            <ul class="dropdown-menu bg-dark" aria-labelledby="dropdownMenu">
              {#if !isLogged}
                <li>
                  <Link to="/login" class="dropdown-item">Iniciar sesión</Link>
                </li>
                <li>
                  <Link to="/singup" class="dropdown-item">Crear cuenta</Link>
                </li>
              {:else}
                <li>
                  <Link to="/account" class="dropdown-item">Mi cuenta</Link>
                </li>
                {#if isProfessor}
                  {#if user.id_autoescuela}
                    <li>
                      <Link to="/profesor" class="dropdown-item"
                        >Soy profesor</Link
                      >
                    </li>
                  {/if}
                {:else if user.id_autoescuela}
                  <li>
                    <Link to="/alumno" class="dropdown-item">Soy alumno</Link>
                  </li>
                {/if}
                <li>
                  <Link to="/logout" class="dropdown-item">Cerrar sesión</Link>
                </li>
              {/if}
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</div>
<br />
