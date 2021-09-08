<script>
  import Puzzle from '$lib/Puzzle.svelte'
</script>

<main>
  <Puzzle />
</main>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    background-color: #f8f8f8;
  }
  main {
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
</style>
