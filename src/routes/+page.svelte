<script lang="ts">

</script>

<div class="container">
  <!-- Recursos, Espacios Físicos, Estados, Roles, Clientes y Reservas -->
  <div class="buttons">
    <a href="/recursos" class="btn btn-recursos">
      <i class="fas fa-warehouse"></i>
      <span>Recursos</span>
    </a>
    <a href="/espacios-fisicos" class="btn btn-espacios-fisicos">
      <i class="fas fa-building"></i>
      <span>Espacios Físicos</span>
    </a>
    <a href="/estados" class="btn btn-estados">
      <i class="fas fa-map-marker-alt"></i>
      <span>Estados</span>
    </a>
    <a href="/roles" class="btn btn-roles">
      <i class="fas fa-users"></i>
      <span>Roles</span>
    </a>
    <a href="/clientes" class="btn btn-clientes">
      <i class="fas fa-user"></i>
      <span>Clientes</span>
    </a>
    <a href="/reservas" class="btn btn-reservations">
      <i class="far fa-calendar-alt"></i>
      <span>Reservas</span>
    </a>
  </div>
</div>

<style>
  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .buttons {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  }
  
  .btn {
    margin: 0 10px;
    border-radius: 10px;
    width: 200px;
    height: 200px;
    font-size: 1.5rem;
    font-weight: bold;
    text-transform: uppercase;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-decoration: none;
    margin-top: 10px;
    margin-bottom: 10px;
  }
  
  .btn i {
    font-size: 3rem;
    margin-bottom: 10px;
  }
  
  .btn-roles {
    background-color: #007bff;
    color: #fff;
  }
  
  .btn-estados {
    background-color: #28a745;
    color: #fff;
  }

  .btn-reservations {
    background-color: #dc3545;
    color: #fff;
  }

  .btn-recursos {
    background-color: #ffc107;
    color: #fff;
  }

  .btn-espacios-fisicos {
    background-color: #17a2b8;
    color: #fff;
  }

  .btn-clientes {
    background-color: #6c757d;
    color: #fff;
  }

  .btn:hover {
    opacity: 0.8;
  }

  /* On smaller screens, make it two rows */
  @media screen and (max-width: 600px) {
    .btn {
      width: 80%;
    }

    .container {
      height: 100%;
    }
  }

</style>