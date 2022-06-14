<script lang="ts">
  import Board from '../lib/Board.svelte'

  const rows = [1, 2, 3, 4, 5, 6, 7, 8].reverse()
  const columns = 'ABCDEFGH'.split('')
  const board = rows.map(row => columns.map(column => column + row))

  type Coords = {
    x: number
    y: number
  }

  let position: Coords | null = null
  let history: string[] = []
  let delayMs = 500

  let instructions = ''
  const instructionToMove: Record<string, [number, number]> = {
    A: [-1, -2],
    B: [1, -2],
    C: [2, -1],
    D: [2, 1],
    E: [1, 2],
    F: [-1, 2],
    G: [-2, 1],
    H: [-2, -1],
  }

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

  function uppercase(node: HTMLInputElement) {
    const transform = () => (node.value = node.value.toUpperCase())
    node.addEventListener('input', transform, {capture: true})
    transform()
  }
</script>

<main>
  <div class="board">
    <Board selected={position} onCellClick={({x, y}) => onCellClick({x, y})} />
  </div>
  <div class="controls">
    <div>
      <label for="instructions">Instructions</label>
      <input id="instructions" type="text" use:uppercase bind:value={instructions} />
    </div>
    <button on:click={onGoClick}>Go</button>
    <button on:click={onResetClick}>Reset</button>
    <div>
      <label for="delay">Delay (ms)</label>
      <input id="delay" type="number" bind:value={delayMs} />
    </div>
    <pre class="history">
      {JSON.stringify(history, null, 2)}
    </pre>
  </div>
</main>

<style>
  main {
    display: flex;
    gap: 2rem;
  }
  .board {
    flex: 1;
    max-width: 768px;
  }

  .controls {
    margin-top: 2rem;
  }
  .controls div {
    display: inline-block;
  }
  .controls label {
    display: block;
    font-size: 0.8em;
  }

  .history {
    margin-top: 2rem;
    white-space: pre-line;
  }
</style>
