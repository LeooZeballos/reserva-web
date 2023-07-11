<script lang="ts">
	import { API_URL } from "../../../consts";

  let name: string;
  let description: string;

  const onSubmit = async (event: Event) => {
    event.preventDefault();

    let data = {
      nombre: name,
      descripcion: description
    };

    await fetch(
      API_URL + '/rol', {
      method: 'POST',
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
      <h1>Crear Rol</h1>
    </div>
    <div class="col-12">
      <form on:submit|preventDefault={onSubmit}>
        <div class="mb-3">
          <label for="name" class="form-label">Nombre</label>
          <input
            type="text"
            class="form-control"
            id="name"
            aria-describedby="nameHelp"
            bind:value={name}
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
            bind:value={description}
            minlength="3"
            maxlength="255"
            required
          >
          <div id="descriptionHelp" class="form-text">La descripción del rol.</div>
        </div>
        <button type="submit" class="btn btn-primary">Crear</button>
      </form>
    </div>
  </div>
</div>

<style>
  .container {
    padding-top: 20px;
  }
</style>