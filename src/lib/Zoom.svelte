<script>
  import { onMount } from 'svelte'

  let el
  let scene
  let zoom = 1
  let ready = false

  const handleResize = () => {
    const child = el.firstChild
    if (!child) return

    const smallestDim = Math.min( child.scrollWidth, child.scrollHeight)
    const padding = smallestDim > 900 ? 20 : 5

    const ratio = Math.min(
      (el.offsetWidth - padding) / child.scrollWidth,
      (el.offsetHeight - padding) / child.scrollHeight,
    )
    zoom = ratio
    ready = true
  }

  onMount(handleResize)
</script>

<svelte:window on:resize={handleResize} />

<div class="outter" bind:this={el}>
  <div
    bind:this={scene}
    class="scene"
    class:ready
    style="transform: scale({zoom})"
  >
    <slot />
  </div>
</div>

<style>
  .outter {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    overflow-x: hidden;

    display: flex;
    justify-content: center;
  }
  .scene {
    transform-origin: top;
  }
  .scene:not(.ready) {
    visibility: hidden;
  }
</style>
