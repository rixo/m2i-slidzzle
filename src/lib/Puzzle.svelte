<script>
  const squareSize = 100
  const size = 4
  const shuffleMoves = 200

  const shiftImage = (x, y) =>
    `background-position: left -${x * squareSize}px top -${y * squareSize}px`

  const squares = Array.from({length: size * size}).map((_, i) => {
    const x = i % size
    const y = Math.floor(i / size)
    return {
      id: i,
      label: "",
      label: i + 1,
      x,
      y,
      style: shiftImage(x, y),
    }
  })
  squares.pop()

  let grid = Array.from({length: size}).map(
    (_, j) => Array.from({length: size}).map((_, i) => squares[j * size + i])
  )

  let blankIndex = squares.length
  let blankX, blankY

  const updateBlank = () => {
    blankX = blankIndex % size
    blankY = Math.floor(blankIndex / size)
  }

  $: blankIndex, updateBlank()

  const updateSquares = () => {
    grid.forEach((line, y) => {
      line.forEach((square, x) => {
        if (!square) return
        square.activable = (
          y === blankY && (x === blankX - 1 || x === blankX + 1)
          || x === blankX && (y === blankY - 1 || y === blankY + 1)
        )
      })
    })
    grid = grid
  }

  const move = (square, x, y) => {
    grid[blankY][blankX] = square
    grid[y][x] = null
    grid = grid
    blankIndex = y * size + x
  }

  const checkVictory = () =>
    grid.every(
      (line, y) => line.every(
        (square, x) => !square || square.x === x && square.y === y
      )
    )


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
  }

  const solve = () => {
    grid[size - 1][size - 1] = null
    squares.forEach((square) => {
      grid[square.y][square.x] = square
    })
    grid = grid
    blankIndex = squares.length
  }

</script>

<h1 class:hidden={!victory}>Victoire&nbsp;!</h1>

<div class="frame" style="width: {squareSize * size}px; height: {squareSize * size}px;">
  {#each grid as line, y (y)}
    {#each line as square, x (square?.id)}
      {#if square}
        <div
          class="square"
          class:activable={square.activable}
          style="left: {x * squareSize}px; top: {y * squareSize}px; {square.style}"
          on:click={square.activable ? () => move(square, x, y) : null}
        >
          {square.label}
        </div>
      {:else}
        <div class="square blank" style="left: {blankX * squareSize}px; top: {blankY * squareSize}px;"></div>
      {/if}
    {/each}
  {/each}
</div>

<div class="buttons">
  <button on:click={() => shuffle()}>Shuffle</button>
  <button on:click={solve}>Solve</button>
</div>

<style>
  h1.hidden {
    visibility: hidden;
  }
  .frame {
    --border-color: lightgrey;
    --border: 1px solid var(--border-color);
    border: var(--border);
    display: flex;
    flex-wrap: wrap;
    position: relative;
  }
  .square {
    position: absolute;
    transition: all 200ms;

    box-sizing: border-box;
    border: var(--border);
    width: 100px;
    height: 100px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #fff;

    font-weight: bold;
    color: #0f0;
  }
  .square.activable:hover {
    cursor: pointer;
    opacity: .75;
  }
  .square {
    background: url(https://baconmockup.com/400/400/);
  }
  .square.blank {
    background: var(--border-color);
    background: transparent;
    transition: none;
  }

  .buttons {
    margin: 1rem 0;
  }
</style>
