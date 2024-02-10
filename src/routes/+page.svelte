<script lang="ts">
  import { emoji } from "./emoji";
  type State = "start" | "playing" | "paused" | "won" | "lost";
  let state: State = "start";
  let size = 20;
  let grid = createGrid();
  let maxMatches = grid.length / 2;
  let selected: number[] = [];
  let matches: string[] = [];
  let timerId: number | null = null;
  let time = 60;

  function resetGame() {
    timerId && clearInterval(timerId);
    grid = createGrid();
    maxMatches = grid.length / 2;
    selected = [];
    matches = [];
    timerId = null;
    time = 60;
  }

  function startGameTimer() {
    function countdown() {
      state !== "paused" && (time -= 1);
    }
    timerId = setInterval(countdown, 1000);
  }
  function gameLost() {
    state = "lost";
    resetGame();
  }
  $: if (state === "playing") {
    //	in case you pause the game
    !timerId && startGameTimer();
  }
  $: time === 0 && gameLost();
  function createGrid() {
    // only want unique cards
    let cards = new Set<string>();
    // half because we duplicate the cards
    let maxSize = size / 2;

    while (cards.size < maxSize) {
      // pick random emoji
      const randomIndex = Math.floor(Math.random() * emoji.length);
      cards.add(emoji[randomIndex]);
    }

    // duplicate and shuffle cards
    return shuffle([...cards, ...cards]);
  }

  function shuffle<Items>(array: Items[]) {
    return array.sort(() => Math.random() - 0.5);
  }
  function selectCard(cardIndex: number) {
    selected = selected.concat(cardIndex);
  }
  function matchCards() {
    const [first, second] = selected;

    if (grid[first] === grid[second]) {
      matches = matches.concat(grid[first]);
    }
    setTimeout(() => (selected = []), 300);
  }
  function gameWon() {
    state = "won";
    resetGame();
  }
  $: selected.length === 2 && matchCards();
  $: maxMatches === matches.length && gameWon();
</script>

{#if state === "start"}
  <h1>Matching game</h1>
  <button on:click={() => (state = "playing")}> Play </button>
{/if}

{#if state === "playing"}
  <h1 class="timer" class:pulse={time <= 10}>
    {time}
  </h1>
  <div class="cards">
    {#each grid as card, cardIndex}
      {@const isSelected = selected.includes(cardIndex)}
      {@const isSelectedOrMatch =
        selected.includes(cardIndex) || matches.includes(card)}
      {@const match = matches.includes(card)}
      <button
        class="card"
        class:selected={isSelected}
        disabled={isSelectedOrMatch}
        on:click={() => selectCard(cardIndex)}
      >
        <div class="matches">
          {#each matches as match}
            <div>{match}</div>
          {/each}
        </div>
      </button>
    {/each}
  </div>
{/if}

{#if state === "lost"}
  <h1>You lost! 💩</h1>
  <button on:click={() => (state = "playing")}> Play again </button>
{/if}

{#if state === "won"}
  <h1>You win! 🎉</h1>
  <button on:click={() => (state = "playing")}> Play again </button>
{/if}

<style>
  .cards {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 0.4rem;
  }

  .card {
    height: 140px;
    width: 140px;
    font-size: 4rem;
    background-color: var(--bg-2);

    &.selected {
      border: 4px solid var(--border);
    }
    & .match {
      transition: opacity 0.3s ease-out;
      opacity: 0.4;
    }
  }
  h1 + button {
    width: max-content;
    margin-top: 2rem;
    margin-inline: auto;
    border: 4px solid var(--border);
  }

  button {
    padding: 1.5rem;
    font-size: 2rem;
    font-weight: 900;
    color: inherit;
    background: none;
    border-radius: 8px;
    border: none;
    text-transform: uppercase;
    cursor: pointer;
  }
  .matches {
    display: flex;
    gap: 1rem;
    margin-block: 2rem;
    font-size: 3rem;
  }
  .timer {
    transition: color 0.3s ease;
  }

  .pulse {
    color: var(--pulse);
    animation: pulse 1s infinite ease;
  }

  @keyframes pulse {
    to {
      scale: 1.4;
    }
  }
</style>
