<script lang="ts">
  import { onMount } from 'svelte';
  import { API_URL, PAGING_SIZE, SORT_DEFAULT, SORT_FIELD_DEFAULT } from '../../consts';
  import type { Reserva } from '../../types';

  let reservas: Reserva[] = [];

  let pageNumber: number = 0;
  let totalPages: number = 0;

  let sortBy: string = SORT_DEFAULT;
  let sortField: string = SORT_FIELD_DEFAULT;

  let filterType: string = '';
  let searchContent: string = '';

  onMount(async () => {
    await loadReservas();
  });

  const loadReservas = async () => {
    let query = API_URL + '/reserva' +
      '?page=' + pageNumber + 
      '&size=' + PAGING_SIZE +
      '&sort=' + sortField + ',' + sortBy;

    // Apply search filters
    if (filterType && searchContent) {
      query += '&' + filterType + '=' + searchContent;
    }

    let data = await fetch(query).then((x) => x.json());
    reservas = data.content;
    totalPages = data.totalPages;
  }

  const handleFilterChange = (event: Event) => {
    let target = event.target as HTMLSelectElement;
    filterType = target.value;
  }

  const handleSearch = async () => {
    if (!filterType || !searchContent) {
      alert("Debe seleccionar un filtro y escribir un texto para buscar");
      return;
    }

    pageNumber = 0;
    await loadReservas();
  }

  const handleSortChange = () => {
    loadReservas();
  }

  const onClearAll = async () => {
    filterType = '';
    searchContent = '';
    pageNumber = 0;
    await loadReservas();
  }

  const parseDate = (date: Date) => {
    let dateString: string = date.toString();
    return new Date(dateString).toLocaleDateString('es-ES', { day: '2-digit', month: '2-digit', year: 'numeric' });
  }

  const parseTime = (horaInicio: Date, horaFin: Date) => {
    let horaInicioString: string = horaInicio.toString();
    let horaFinString: string = horaFin.toString();
    return new Date(horaInicioString).toLocaleTimeString('es-ES', { hour: '2-digit', minute: '2-digit' }) + 
      " - " + new Date(horaFinString).toLocaleTimeString('es-ES', { hour: '2-digit', minute: '2-digit' });
  }

  // parse minutos, for example: 60 -> 1:00, 90 -> 1:30
  const parseMinutes = (minutes: number) => {
    let hours = Math.floor(minutes / 60);
    let minutesLeft = minutes % 60;
    return hours + ":" + minutesLeft.toString().padStart(2, '0');
  }

  const onNextPage = () => {
    pageNumber++;
    loadReservas();
  }

  const onPreviousPage = () => {
    pageNumber--;
    loadReservas();
  }

  const deleteReserva = (id: number) => {
    // Borrar la reserva
    return async () => {
      // Pedir por confirmación
      let confirmed = await confirm("¿Está seguro que desea eliminar la reserva con id=" + id + "?");
      if (confirmed) alert("Se eliminó la reserva con id=" + id);
      else return;

      let query = API_URL + '/reserva' + id;

      // Llamar a la API para borrar la reserva
      await fetch(query, { method: 'DELETE' });

      // Recargar los reservas
      await loadReservas();
    }
  }
</script>

<div class="container">
  <div class="row">
    <!-- Title -->
    <div class="col-12">
      <div class="alert alert-primary" role="alert">
        <h4 class="alert-heading">Reservas</h4>
        <p>Las reservas de un Espacio Físico realizadas por un Cliente.</p>
        <div class="col-12">
          <a href="/reservas/create" class="btn btn-primary disabled">Crear Reserva</a>
          <a href="/" class="btn btn-secondary">Volver al inicio</a>
        </div>
      </div>
    </div>

    <!-- Search -->
    <div class="search-bar" style="margin-bottom: 20px;">
      <div class="input-group mb-">
        <span class="input-group-text" id="inputGroup-sizing-default">Buscar...</span>
        <input 
          type="text"
          class="form-control"
          id="search"
          aria-label="Sizing example input"
          aria-describedby="inputGroup-sizing-default"
          bind:value={searchContent}
          on:keydown={e => e.key === 'Enter' ? handleSearch() : null}
        />
        <select class="form-control" on:change={handleFilterChange}>
          <option value="" disabled selected>Tipo</option>
          <option value="duracion">Duración</option>
          <option value="comentario">Comentario</option>
          <option value="clienteId">Cliente</option>
          <option value="motivoReserva">Motivo de Reserva</option>
          <option value="estadoId">Estado</option>
          <option value="motivoRechazo">Motivo de Rechazo</option>
          <option value="espacioFisicoId">Espacio Físico</option>
        </select>
        <button class="btn btn-primary" on:click={handleSearch}><i class="fas fa-search"></i></button>
      </div>
    </div>

    <!-- Sort -->
    <div class="sort-bar" style="margin-bottom: 20px;">
      <div class="input-group mb-3">
        <span class="input-group-text" id="inputGroup-sizing-default">Orderar por: </span>
        <select class="form-control" bind:value={sortBy} on:change={handleSortChange} id="sort">
          <option value="asc">Ascendente</option>
          <option value="desc">Descendente</option>
        </select>
      </div>
      <div class="input-group mb-3">
        <span class="input-group-text" id="inputGroup-sizing-default">Campo: </span>
        <select class="form-control" bind:value={sortField} on:change={handleSortChange} id="sortField">
          <option value="id">ID</option>
          <option value="fechaHoraInicio">Fecha y Hora de Inicio</option>
          <option value="fechaHoraFin">Fecha y Hora de Fin</option>
        </select>
      </div>
    </div>
    
    <!-- Table -->
    <table class="table table-list">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">Espacio Físico</th>
          <th scope="col">Cliente</th>
          <th scope="col">FechaInicio</th>
          <th scope="col">FechaFin</th>
          <th scope="col">Horario</th>
          <th scope="col">Duración</th>
          <th scope="col">Estado</th>
          <th scope="col" class="actions-header">Acciones</th>
        </tr>
      </thead>
      <tbody>
        {#each reservas as reserva}
          <tr>
            <th scope="row">{reserva.id}</th>
            <td>{'[' + reserva.espacioFisico.id + '] ' + reserva.espacioFisico.nombre}</td>
            <td>{'[' + reserva.cliente.id + '] ' + reserva.cliente.nombre}</td>
            <td>{parseDate(reserva.fechaHoraInicio)}</td>
            <td>{parseDate(reserva.fechaHoraFin)}</td>
            <td>{parseTime(reserva.fechaHoraInicio, reserva.fechaHoraFin)}</td>
            <td>{parseMinutes(reserva.duracion)}</td>
            <td>{'[' + reserva.estado.id + '] ' + reserva.estado.nombre}</td>
            <td class="actions">
              <a href="/reservas/{reserva.id}/edit" class="btn btn-primary disabled">Editar</a>
              <button class="btn btn-danger" on:click={deleteReserva(reserva.id)}>Eliminar</button>
            </td>
          </tr>
        {/each}
      </tbody>
    </table>

    <!-- Pagination -->
    <div class="page-bottom">
      <div class="clear">
        <button class="btn btn-primary" on:click={onClearAll}>Limpiar filtros</button>
      </div>
      <div class="page-info-buttons">
        <div class="input-group">
          <span class="input-group-text" id="inputGroup-sizing-default">Página {pageNumber + 1} de {totalPages}</span>
          <button class="btn btn-primary" disabled={pageNumber == 0} on:click={onPreviousPage}>Anterior</button>
          <button class="btn btn-primary" disabled={pageNumber == totalPages - 1} on:click={onNextPage}>Siguiente</button>
        </div>
      </div>
    </div>
    
  </div>
</div>

<style>

</style>