<script>
  import './styles.scss';
</script>

<nav class="navbar navbar-expand-lg bg-body-tertiary">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">Reserva</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link" href="/recursos">Recursos</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/espacios-fisicos">Espacios Físicos</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/estados">Estados</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/roles">Roles</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/clientes">Clientes</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/reservas">Reservas</a>
        </li>
      </ul>
    </div>
  </div>
</nav>

<slot />

<footer class="footer mt-auto py-3 bg-body-tertiary">
  <div class="container-fluid">
    <span class="text-muted text-center">Reserva © 2023</span>
  </div>
</footer>