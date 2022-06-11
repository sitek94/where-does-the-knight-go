<script lang="ts">
  import Board from '../lib/Board.svelte'

  const rows = [1, 2, 3, 4, 5, 6, 7, 8].reverse()
  const columns = 'abcdefgh'.split('')
  const board = rows.map(row => columns.map(column => column + row))

  type Coords = {
    x: number
    y: number
  }
  let position: Coords | null = null
  let history: string[] = []
  let delayMs = 500

  function onCellClick(coords: Coords) {
    position = coords
  }

  function onResetClick() {
    position = null
    history = []
  }

  async function onGoClick() {
    for (let instr of instructions) {
      const coords = instructionToMove[instr]
      if (position) {
        const newPos = await moveRelative(coords)
        const cell = board[newPos.y][newPos.x]
        history = [...history, cell]
      }
    }
  }

  let instructions = ''
  const instructionToMove: Record<string, [number, number]> = {
    a: [-1, -2],
    b: [1, -2],
    c: [2, -1],
    d: [2, 1],
    e: [1, 2],
    f: [-1, 2],
    g: [-2, 1],
    h: [-2, -1],
  }

  async function moveRelative(coords: [number, number]) {
    if (delayMs) {
      await wait(delayMs)
    }
    const [x, y] = coords
    const newPosition = {
      x: position!.x + x,
      y: position!.y + y,
    }
    position = newPosition
    return newPosition
  }

  function wait(ms: number) {
    return new Promise(resolve => setTimeout(resolve, ms))
  }
</script>

<div class="grid">
  {#each board as rows, y}
    {#each rows as _, x}
      <button class="cell" on:click={() => onCellClick({x, y})}>
        {#if x === position?.x && y === position?.y}
          <span role="img" class="icon">🐴</span>
        {/if}
      </button>
    {/each}
  {/each}
</div>

<div class="controls">
  <div>
    <label for="instructions">Instructions</label>
    <input id="instructions" type="text" bind:value={instructions} />
  </div>
  <button on:click={onGoClick}>Go</button>
  <button on:click={onResetClick}>Reset</button>
  <div>
    <label for="delay">Delay (ms)</label>
    <input id="delay" type="number" bind:value={delayMs} />
  </div>
</div>

<pre class="history">
  {JSON.stringify(history, null, 2)}
</pre>

<style>
  .grid {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
  }

  .cell {
    border: 1px solid #ccc;
    aspect-ratio: 1;
  }

  .icon {
    font-size: 2em;
  }

  .controls {
    margin-top: 1rem;
  }
  .controls div {
    display: inline-block;
  }
  .controls label {
    display: block;
    font-size: 0.8em;
  }

  .history {
    white-space: pre-line;
  }
</style>
