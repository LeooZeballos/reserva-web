<script>
  import { page } from '$app/stores';
</script>

<div class="container">
  <h1>{$page.status}: {$page.error.message}</h1>
  <a
    class="btn btn-secondary"
    href="/"
    on:click={() => page.set({ status: 200 })}
  >
    Go to home
  </a>
</div>

<style>
  .container {
    margin: 20px auto;
  }
  
</style>