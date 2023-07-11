<script lang="ts">
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import { API_URL } from '../../../../consts';

  let id: string | undefined;
  let parsedId: number;
  let nombre: string = '';
  let descripcion: string = '';

  onMount(async () => {
    await loadRole();
  });

  // Subscribe to the $page store to get the current route params
  page.subscribe(($page) => {
    const { params } = $page;
    id = params.id; // Access the "id" parameter from the route
  });

  // Convert the id to a number if it has a valid value
  if (id !== undefined) {
    parsedId = Number(id);
  }

  // Load the role from the API
  const loadRole = async () => {
    let data = await fetch(API_URL + '/rol/' + parsedId)
    .then((x) => x.json());
    nombre = data.nombre;
    descripcion = data.descripcion;
  }

  // Update the role
  const onSubmit = async () => {
    let data = {
      id: parsedId,
      nombre: nombre,
      descripcion: descripcion
    };
    await fetch(API_URL + '/rol/' + parsedId, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });
    window.location.href = '/roles';
  }
</script>

<div class="container">
  <div class="row">
    <div class="col-12">
      <h1>Editar Rol</h1>
    </div>
    <div class="col-12">
      <form on:submit|preventDefault={onSubmit}>
        <div class="mb-3">
          <label for="id" class="form-label">ID</label>
          <input
            type="text"
            class="form-control"
            id="id"
            aria-describedby="idHelp"
            bind:value={id}
            disabled
          >
          <div id="idHelp" class="form-text">El ID del rol.</div>
        </div>
        <div class="mb-3">
          <label for="name" class="form-label">Nombre</label>
          <input
            type="text"
            class="form-control"
            id="name"
            aria-describedby="nameHelp"
            bind:value={nombre}
            minlength="3"
            maxlength="50"
            required
          >
          <div id="nameHelp" class="form-text">El nombre del rol.</div>
        </div>
        <div class="mb-3">
          <label for="description" class="form-label">Descripción</label>
          <input
            type="text"
            class="form-control"
            id="description"
            aria-describedby="descriptionHelp"
            bind:value={descripcion}
            minlength="3"
            maxlength="255"
            required
          >
          <div id="descriptionHelp" class="form-text">La descripción del rol.</div>
        </div>
        <button type="submit" class="btn btn-primary">Editar</button>
        <a href="/roles" class="btn btn-secondary">Cancelar</a>
      </form>
    </div>
  </div>
</div>

<style>

</style>