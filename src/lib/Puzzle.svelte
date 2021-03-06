<script>
  import { onMount } from 'svelte'

  export let labels = false
  export let size = 3

  const squareSize = 200
  const sizeX = size
  const sizeY = size
  const shuffleMoves = 200

  const shiftImage = (x, y) =>
    `background-position: left -${x * squareSize}px top -${y * squareSize}px`

  const blank = {id: null, blank: true}

  let ready = false

  let squares = Array.from({length: sizeX * sizeY}).map((_, i) => {
    const targetX = i % sizeX
    const targetY = Math.floor(i / sizeY)
    return {
      id: i,
      label: labels ? String(i + 1) : "",
      targetX,
      targetY,
      style: [
        shiftImage(targetX, targetY),
        `width: ${squareSize}px; height: ${squareSize}px`,
        `background-size: ${size * 100}%`,
      ].join(';')
    }
  })
  squares.pop()

  let grid = Array.from({length: sizeY}).map(
    (_, j) => Array.from({length: sizeX}).map((_, i) => squares[j * sizeY + i] || blank)
  )

  let blankIndex = squares.length
  let blankX, blankY

  const updateBlank = () => {
    blankX = blankIndex % sizeX
    blankY = Math.floor(blankIndex / sizeY)
  }

  $: blankIndex, updateBlank()

  const updateSquares = () => {
    grid.forEach((line, y) => {
      line.forEach((square, x) => {
        if (!square) return
        square.x = x
        square.y = y
        square.activable = (
          y === blankY && (x === blankX - 1 || x === blankX + 1)
          || x === blankX && (y === blankY - 1 || y === blankY + 1)
        )
      })
    })
    grid = grid
    squares = squares
  }

  const move = (square, x, y) => {
    square.x = x
    square.y = y
    grid[blankY][blankX] = square
    grid[y][x] = blank
    grid = grid
    squares = squares
    blankIndex = y * sizeY + x
  }

  const checkVictory = () =>
    squares.every(({ x, y, targetX, targetY }) => x === targetX && y === targetY)

  $: blankIndex, updateSquares()

  $: victory = checkVictory(grid)

  const random = items => items[Math.floor(Math.random() * items.length)]

  const shuffleOnce = () => {
    const targets = [
      [blankX, blankY - 1],
      [blankX, blankY + 1],
      [blankX - 1, blankY],
      [blankX + 1, blankY],
    ]
      .map(([x, y]) => {
        const square = grid?.[y]?.[x]
        return square && { square, x, y }
      })
      .filter(Boolean)
    const { square, x, y } = random(targets)
    move(square, x, y)
    updateBlank()
  }

  const shuffle = (n = shuffleMoves) => {
    for (let i = 0; i < n; i++) {
      shuffleOnce()
    }
    ready = true
  }

  const solve = () => {
    grid[sizeY - 1][sizeX - 1] = blank
    squares.forEach((square) => {
      grid[square.targetY][square.targetX] = square
    })
    grid = grid
    squares = squares
    blankIndex = squares.length
  }

  onMount(shuffle)

  if (typeof window !== "undefined") {
    window.slidzzle = { solve }
  }
</script>

<div class="game" class:victory={ready && victory} >
  <div class="victory-text">
    <h2 >Bravo&nbsp;! 🎉</h2>
  </div>

  <div class="frame" style="width: {squareSize * sizeX + 2}px; height: {squareSize * sizeY + 2}px;">
    <div class="board">
      {#if ready}
        {#each squares as square (square.id)}
          <div
            class="square"
            class:blank={square.blank}
            class:activable={square.activable}
            style="left: {square.x * squareSize}px; top: {square.y * squareSize}px; {square.style}"
            on:click={square.activable ? () => move(square, square.x, square.y) : null}
          >
            {#if !victory}
              {square.label}
            {/if}
          </div>
        {/each}
      {/if}
    </div>
  </div>

  <div class="victory-text victory-secondary">
    <p>Le chiffre secret est&nbsp: <strong>1</strong></p>
  </div>
</div>

<!--
<div class="buttons">
  <button on:click={() => shuffle()}>Shuffle</button>
  <button on:click={solve}>Solve</button>
</div>
-->

<style>
  .frame {
    --border-color: #A2CDBE;
    --border: 1px solid var(--border-color);
    border: 8px solid var(--primary);
    background-color: #A2CDBE;
    padding: 2px 0 0 2px;
  }
  .board {
    display: flex;
    flex-wrap: wrap;
    position: relative;
  }
  .square {
    position: absolute;
    transition: all 200ms;

    border: var(--border);
    border: 2px solid var(--accent1);
    box-sizing: border-box;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #fff;

    font-weight: bold;
    color: #fff;
    font-size: 32px;
    text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
  }
  .square.activable:hover {
    cursor: pointer;
    opacity: .75;
  }
  .square {
    background: url(https://baconmockup.com/500/500/);
    background: url(/square.jpg);
  }
  .square.blank {
    display: none;
  }

  .victory-text {
    color: var(--accent2);
    opacity: 0;
    font-size: 3rem;
    margin: 2rem 0 1rem;
    text-align: center;
    transition: opacity 500ms ease-out;
  }
  .victory .victory-text {
    visibility: visible;
    opacity: 1;
    transition-timing-function: ease-in;
    transition-duration: 200ms;
  }
  .victory .square {
    border-color: transparent;
  }

  .victory-secondary {
    transition-delay: 0ms;
  }
  .victory .victory-secondary {
    transition-delay: 1000ms;
  }
</style>
