<script lang="ts">
  import { onMount } from 'svelte';
  import { API_URL, PAGING_SIZE, SORT_DEFAULT, SORT_FIELD_DEFAULT } from '../../consts';
  import type { Recurso } from '../../types';

  let recursos: Recurso[] = [];

  let pageNumber: number = 0;
  let totalPages: number = 0;

  let sortBy: string = SORT_DEFAULT;
  let sortField: string = SORT_FIELD_DEFAULT;

  let filterType: string = '';
  let searchContent: string = '';

  onMount(async () => {
    await loadRecursos();
  });

  const loadRecursos = async () => {
    let query = API_URL + '/recurso' +
      '?page=' + pageNumber + 
      '&size=' + PAGING_SIZE +
      '&sort=' + sortField + ',' + sortBy;

    // Apply search filters
    if (filterType && searchContent) {
      query += '&' + filterType + '=' + searchContent;
    }

    let data = await fetch(query).then((x) => x.json());
    recursos = data.content;
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
    await loadRecursos();
  }

  const handleSortChange = () => {
    loadRecursos();
  }

  const onClearAll = async () => {
    filterType = '';
    searchContent = '';
    pageNumber = 0;
    await loadRecursos();
  }

  const onNextPage = () => {
    pageNumber++;
    loadRecursos();
  }

  const onPreviousPage = () => {
    pageNumber--;
    loadRecursos();
  }

  const deleteRecurso = (id: number) => {
    // Borrar el recurso
    return async () => {
      // Pedir por confirmación
      let confirmed = await confirm("¿Está seguro que desea eliminar el recurso " + id + "?");
      if (confirmed) alert("Se eliminó el recurso " + id + " correctamente");

      // Llamar a la API para borrar el recurso
      await fetch(API_URL + '/recurso/' + id, {
        method: 'DELETE'
      });

      // Recargar los recursos
      await loadRecursos();
    }
  }
</script>

<div class="container">
  <div class="row">
    <!-- Title -->
    <div class="col-12">
      <div class="alert alert-primary" role="alert">
        <h4 class="alert-heading">Recursos</h4>
        <p>Los recursos de la universidad, que pueden estar asociados a uno o más Espaciso Físicos.</p>
        <div class="col-12">
          <a href="/recursos/create" class="btn btn-primary disabled">Crear Recurso</a>
          <a href="/" class="btn btn-secondary">Volver al inicio</a>
        </div>
      </div>
    </div>

    <!-- Search -->
    <div class="search-bar">
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
          <option value="nombre">Nombre</option>
          <option value="descripcion">Descripción</option>
        </select>
        <button class="btn btn-primary" on:click={handleSearch}><i class="fas fa-search"></i></button>
      </div>
    </div>

    <!-- Sort -->
    <div class="sort-bar">
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
          <option value="nombre">Nombre</option>
          <option value="descripcion">Descripción</option>
        </select>
      </div>
    </div>
    
    <!-- Table -->
    <table class="table table-list">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">Nombre</th>
          <th scope="col">Descripción</th>
          <th scope="col" class="actions-header">Acciones</th>
        </tr>
      </thead>
      <tbody>
        {#each recursos as recurso}
          <tr>
            <th scope="row">{recurso.id}</th>
            <td>{recurso.nombre}</td>
            <td>{recurso.descripcion}</td>
            <td class="actions">
              <a href="/recursos/{recurso.id}/edit" class="btn btn-primary disabled">Editar</a>
              <button class="btn btn-danger" on:click={deleteRecurso(recurso.id)}>Eliminar</button>
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