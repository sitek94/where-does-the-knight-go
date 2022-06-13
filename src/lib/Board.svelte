<script lang="ts">
  export let selected: {x: number; y: number} | null = null
  export let onCellClick: (cell: Cell) => void

  const columns = 'ABCDEFGH'.split('')
  const rows = '12345678'.split('').reverse()

  function key(x: number, y: number) {
    return `${x},${y}`
  }

  type Cell = {
    x: number
    y: number
    value: string
  }
  type Board = Record<string, Cell>

  function createBoard(size) {
    const board: Board = {}
    for (let y = 0; y < size; y++) {
      for (let x = 0; x < size; x++) {
        const value = columns[x] + rows[y]
        board[key(x, y)] = {x, y, value}
      }
    }
    return board
  }

  let board = createBoard(8)

  const cells = Object.values(board)
</script>

<div class="grid">
  {#each cells as cell}
    <button class="cell" on:click={() => onCellClick(cell)}>
      {#if cell.x === selected?.x && cell.y === selected?.y}
        <span class="icon" role="img" aria-label="horse face facing left">🐴 </span>
      {/if}
      <span class="label">{cell.value}</span>
    </button>
  {/each}
</div>

<style>
  .grid {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
  }

  .cell {
    position: relative;
    border: 1px solid #ccc;
    aspect-ratio: 1;
  }

  .icon {
    font-size: 2.5em;
  }

  .label {
    position: absolute;
    color: #555;
    bottom: 0.25rem;
    left: 0.25rem;
    font-size: 0.75em;
  }
</style>
