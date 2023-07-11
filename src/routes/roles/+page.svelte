<script lang="ts">
  import { onMount } from 'svelte';
  import { API_URL, PAGING_SIZE, SORT_DEFAULT, SORT_FIELD_DEFAULT } from '../../consts';
  import type { Role } from '../../types';

  let roles: Role[] = [];

  let pageNumber: number = 0;
  let totalPages: number = 0;

  let sortBy: string = SORT_DEFAULT;
  let sortField: string = SORT_FIELD_DEFAULT;

  let filterType: string = '';
  let searchContent: string = '';

  onMount(async () => {
    await loadRoles();
  });

  const loadRoles = async () => {
    let query = API_URL + '/rol' +
      '?page=' + pageNumber + 
      '&size=' + PAGING_SIZE +
      '&sort=' + sortField + ',' + sortBy;
    
    // Apply search filters
    if (filterType && searchContent) {
      query += '&' + filterType + '=' + searchContent;
    }
    
    let data = await fetch(query).then((x) => x.json());
    roles = data.content;
    totalPages = data.totalPages;
  }

  const handleFilterChange = (event: Event) => {
    let target = event.target as HTMLSelectElement;
    filterType = target.value;
  }

  const handleSearch = async (event: Event) => {
    event.preventDefault();

    if (!filterType || !searchContent) {
      alert("Debe seleccionar un filtro y escribir un texto para buscar");
      return;
    }

    pageNumber = 0;
    await loadRoles();
  }

  const handleSortChange = () => {
    loadRoles();
  }

  const onClearAll = async () => {
    filterType = '';
    searchContent = '';
    pageNumber = 0;
    await loadRoles();
  }

  const onNextPage = () => {
    pageNumber++;
    loadRoles();
  }

  const onPreviousPage = () => {
    pageNumber--;
    loadRoles();
  }

  const deleteRol = (id: number) => {
    // Borrar el rol
    return async () => {
      // Pedir por confirmación
      let confirmed = await confirm("¿Está seguro que desea eliminar el rol " + id + "?");
      if (confirmed) alert("Se eliminó el rol " + id + " correctamente");
      else return;

      let query = API_URL + '/rol/' + id;

      // Llamar a la API para borrar el rol
      await fetch(query, {method: 'DELETE'});

      // Recargar los roles
      await loadRoles();
    }
  }
</script>

<div class="container">
  <div class="row">
    <!-- Title -->
    <div class="col-12">
      <div class="alert alert-primary" role="alert">
        <h4 class="alert-heading">Roles</h4>
        <p>Los roles son una forma de agrupar permisos para asignarlos a los usuarios.</p>
        <div class="col-12">
          <a href="/roles/create" class="btn btn-primary">Crear Rol</a>
          <a href="/" class="btn btn-secondary">Volver al inicio</a>
        </div>
      </div>
    </div>

    <!-- Search -->
    <div class="search-bar" style="margin-bottom: 20px;">
      <div class="input-group mb-">
        <span class="input-group-text" id="inputGroup-sizing-default">Buscar...</span>
        <input type="text" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default" bind:value={searchContent} />
        <select class="form-control" on:change={handleFilterChange}>
          <option value="" disabled selected>Tipo</option>
          <option value="nombre">Nombre</option>
          <option value="descripcion">Descripción</option>
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
        <!-- One row for each role -->
        {#each roles as rol}
          <tr>
            <th scope="row">{rol.id}</th>
            <td>{rol.nombre}</td>
            <td>{rol.descripcion}</td>
            <td class="actions">
              <a href="/roles/{rol.id}/edit" class="btn btn-primary">Editar</a>
              <button class="btn btn-danger" on:click={deleteRol(rol.id)}>Eliminar</button>
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