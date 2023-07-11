<script lang="ts">
	import { API_URL } from "../../../consts";

  let nombre: string = '';
  let descripcion: string = '';
  let color: string = '#000000';

  const onSubmit = async (event: Event) => {
    event.preventDefault();

    let body = {
      nombre: nombre,
      descripcion: descripcion,
      color: color.substring(1).toUpperCase()
    }

    await fetch(
      API_URL + '/estado', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(body)
    });

    window.location.href = '/estados';
  }

</script>

<div class="container">
  <div class="row">
    <div class="col-12">
      <h1>Crear Estado</h1>
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
            bind:value={nombre}
            minlength="3"
            maxlength="50"
            required
          >
          <div id="nameHelp" class="form-text">El nombre del estado.</div>
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
          <div id="descriptionHelp" class="form-text">La descripción del estado.</div>
        </div>
        <div class="mb-3">
          <label for="color" class="form-label">Color</label>
          <input
            type="color" 
            class="form-control"
            id="color"
            aria-describedby="colorHelp"
            bind:value={color}
            minlength="3"
            maxlength="255"
            required
          >
          <div id="colorHelp" class="form-text">El color del estado. En formato hexadecimal.</div>
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