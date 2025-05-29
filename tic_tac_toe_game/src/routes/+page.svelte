<script>
  // Minimalist palette and theme (light mode)
  const COLORS = {
    primary: "#ffffff", // board/cell background
    secondary: "#222222", // text and player X color
    accent: "#4caf50" // player O and win indicator
  };

  // Board: a 1D array of 9 cells, null | 'X' | 'O'
  let board = Array(9).fill(null);
  let xIsNext = true;
  let winner = null;
  let draw = false;

  // PUBLIC_INTERFACE
  function getStatus() {
    if (winner) {
      return `Winner: ${winner}`;
    }
    if (draw) {
      return "It's a draw!";
    }
    return `Next: ${xIsNext ? "X" : "O"}`;
  }

  // PUBLIC_INTERFACE
  function handleClick(idx) {
    if (board[idx] || winner) return;
    board = board.slice();
    board[idx] = xIsNext ? "X" : "O";
    xIsNext = !xIsNext;
    winner = calculateWinner(board);
    draw = !winner && board.every(Boolean);
  }

  // PUBLIC_INTERFACE
  function restart() {
    board = Array(9).fill(null);
    xIsNext = true;
    winner = null;
    draw = false;
  }

  // PUBLIC_INTERFACE
  function calculateWinner(b) {
    const lines = [
      [0,1,2],[3,4,5],[6,7,8],  // rows
      [0,3,6],[1,4,7],[2,5,8],  // columns
      [0,4,8],[2,4,6]           // diagonals
    ];
    for (const [a,bIdx,c] of lines) {
      if (b[a] && b[a] === b[bIdx] && b[a] === b[c]) {
        return b[a];
      }
    }
    return null;
  }
</script>

<style>
  .container {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--bg, #fff);
  }
  .game-board {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1.5rem;
    box-shadow: 0 2px 14px rgba(0,0,0,.04);
    border-radius: 14px;
    padding: 2rem;
    background: var(--primary, #fff);
  }
  .status {
    font-size: 1.3rem;
    font-weight: 500;
    margin-bottom: 1rem;
    color: var(--secondary, #222);
    letter-spacing: 0.05em;
    text-align: center;
    min-height: 1.6em;
  }
  .board-grid {
    display: grid;
    grid-template-columns: repeat(3, 64px);
    grid-template-rows: repeat(3, 64px);
    gap: 8px;
    background: var(--primary, #fff);
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(34,34,34,0.04);
  }
  .cell {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 2.1rem;
    font-family: inherit;
    background: var(--primary, #fff);
    border: 1.5px solid #ececec;
    color: var(--secondary, #222);
    border-radius: 8px;
    cursor: pointer;
    user-select: none;
    transition: background .15s, color .15s;
    outline: none;
  }
  .cell:focus-visible {
    outline: 2px solid var(--accent, #4caf50);
  }
  .cell.cell-x {
    color: var(--secondary, #222);
    font-weight: bold;
  }
  .cell.cell-o {
    color: var(--accent, #4caf50);
    font-weight: bold;
  }
  .cell.disabled {
    cursor: not-allowed;
    opacity: 0.6;
  }
  .reset-btn {
    margin-top: .9rem;
    padding: 0.6em 2em;
    font-size: 1rem;
    background: var(--accent, #4caf50);
    color: var(--primary, #fff);
    border: none;
    border-radius: 99em;
    font-weight: 500;
    cursor: pointer;
    letter-spacing: 0.03em;
    transition: background .15s, color .15s, box-shadow .15s;
    box-shadow: 0 2px 4px rgba(76,175,80,0.06);
  }
  .reset-btn:hover {
    background: #388e3c;
  }
</style>

<!-- The theme palette -->
<svelte:head>
  <style>
    html, body {
      --primary: #ffffff;
      --secondary: #222222;
      --accent: #4caf50;
      --bg: #fff;
    }
  </style>
</svelte:head>

<!-- MAIN GAME CONTAINER -->
<div class="container">
  <div class="game-board" style="background: {COLORS.primary}">
    <div class="status" style="color: {winner ? COLORS.accent : COLORS.secondary};">
      {getStatus()}
    </div>
    <div class="board-grid" role="grid" aria-label="TicTacToe board">
      {#each board as cell, idx (idx)}
        <button
          class="cell {cell === 'X' && 'cell-x'} {cell === 'O' && 'cell-o'} {winner || cell ? 'disabled' : ''}"
          type="button"
          disabled={!!winner || !!cell || draw}
          aria-label={"cell " + (idx+1)}
          on:click={() => handleClick(idx)}
        >
          {cell}
        </button>
      {/each}
    </div>
    <button class="reset-btn" on:click={restart} type="button">
      Restart
    </button>
  </div>
</div>
