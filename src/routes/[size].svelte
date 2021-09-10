<script>
  import { page } from '$app/stores'

  import Puzzle from '$lib/Puzzle.svelte'
  import Zoom from '$lib/Zoom.svelte'

  export let easy = false

  $: size = Math.min(10, Math.max(3, $page.params.size))
</script>

<main>
  <Zoom>
    <Puzzle {size} labels={easy} />
  </Zoom>
</main>

<style>
  main {
    display: flex;
    align-self: stretch;
    position: relative;
  }
  main > :global(*) {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
  }
</style>
