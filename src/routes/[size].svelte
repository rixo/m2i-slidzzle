<script>
  import { page } from '$app/stores'

  import Puzzle from '$lib/Puzzle.svelte'

  export let easy = false

  $: size = Math.min(10, Math.max(3, $page.params.size))
</script>

<main>
  <Puzzle {size} labels={easy} />
</main>

<style>
  main {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
