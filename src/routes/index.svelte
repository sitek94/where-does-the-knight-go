<script lang="ts">
  const rows = [1, 2, 3, 4, 5, 6, 7, 8].reverse()
  const columns = 'abcdefgh'.split('')
  const board = rows.map(row => columns.map(column => column + row))

  let position = ''

  function onCellClick(cell: string) {
    position = cell
  }

  function onResetClick() {
    position = ''
  }
</script>

<div class="grid">
  {#each board as row}
    {#each row as cell}
      <button class="cell" on:click={() => onCellClick(cell)}>
        {#if cell === position}
          <span role="img" class="icon">🐴</span>
        {/if}
      </button>
    {/each}
  {/each}
</div>

<div class="controls">
  <button on:click={onResetClick}>Reset</button>
</div>

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
</style>
