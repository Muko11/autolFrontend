<script>
    import { Link } from "svelte-routing";
</script>

<!-- Footer -->

<footer class="text-center text-white bg-dark">
    <div class="container-fluid">
        <div class="mt-5">
            <div
                class="row text-center d-flex justify-content-center align-items-center pt-5"
            >
                <div class="col-md-2">
                    <h6>
                        <a href="#" class="enlace-footer">Política de datos</a>
                    </h6>
                </div>
                <div class="col-md-2">
                    <h6>
                        <a href="#" class="enlace-footer">Política de cookies</a
                        >
                    </h6>
                </div>
            </div>
        </div>

        <hr class="my-4" />

        <div class="mb-5">
            <div class="row d-flex justify-content-center">
                <div class="col-lg-8">
                    <h4>AutoL</h4>
                    <p class="lead">
                        Organiza tu autoescuela de forma sencilla, rápida y
                        fácil
                    </p>
                    <Link to="/" class="navbar-brand"
                        ><img
                            class="iconos"
                            src="imagenes/logo.svg"
                            alt="logo"
                        /></Link
                    >
                </div>
            </div>
        </div>
        <div class="text-center mb-5">
            <div class="redes">
                <a href="#" style="text-decoration: none;">
                    <i class="fa-brands fa-twitter fa-2x" />
                    <!-- <img class="iconos" src="imagenes/twitter.png" alt="Twitter"> -->
                </a>
                <a href="#" style="text-decoration: none">
                    <!-- <img class="iconos" src="imagenes/facebook.png" alt="Facebook"> -->
                    <i class="fa-brands fa-youtube fa-2x" />
                </a>
                <a href="#" style="text-decoration: none">
                    <!--  <img class="iconos" src="imagenes/youtube.png" alt="Youtube"> -->
                    <i class="fa-brands fa-instagram fa-2x" />
                </a>
                <a href="#" style="text-decoration: none">
                    <!-- <img class="iconos" src="imagenes/linkedin.png" alt="Linkedin"> -->
                    <i class="fa-brands fa-linkedin-in fa-2x" />
                </a>
            </div>
        </div>
    </div>
    <div class="text-center p-3" style="background-color: rgba(0, 0, 0, 0.2)">
        &copy; 2023 Copyright - AutoL
    </div>
</footer>
