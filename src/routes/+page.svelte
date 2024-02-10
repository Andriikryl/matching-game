<script lang="ts">
  import { emoji } from "./emoji";
  type State = "start" | "playing" | "paused" | "won" | "lost";
  let state: State = "start";
  let size = 20;
  let grid = createGrid();
  let maxMatches = grid.length / 2;
  let selected: number[] = [];
  let matches: string[] = [];
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
</script>

<div>das</div>
