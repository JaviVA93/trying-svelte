<script lang="ts">
  import Weather from "./components/Weather.svelte";
</script>

<main>
  <Weather />
</main>

<style>
  main {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
      Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
      sans-serif;
    width: 100vw;
    height: 100vh;
    background-color: rgb(41, 40, 40);
    color: rgb(219, 219, 219);
    padding: 20px;
  }
</style>
