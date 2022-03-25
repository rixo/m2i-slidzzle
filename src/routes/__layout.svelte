<div class="page">
  <img class="logo" src="/logo.png" alt="2itech Academy by M2i" />
  <slot>
    <main />
  </slot>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    background-color: #f8f8f8;

    --primary: #303366;
    --accent1: #A2CDBE;
    --accent2: #B65593;
  }

  .logo {
    height: 100px;
  }

  .page {
    min-width: 100vw;
    min-height: 100vh;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    font-family: sans-serif;

    padding: 1rem 0;
  }
  .page > :global(main) {
    flex: 1;
  }
</style>
