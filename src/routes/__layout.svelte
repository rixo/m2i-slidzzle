<div class="page">
  <img class="logo" src="/logo.png" />
  <slot>
    <main />
  </slot>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    background-color: #f8f8f8;
  }

  .logo {
    height: 100px;
    margin-top: 2rem;
  }

  .page {
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    font-family: sans-serif;
  }
  .page > :global(main) {
    flex: 1;
  }
  main {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
