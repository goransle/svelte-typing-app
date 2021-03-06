<script lang="ts">
  import type {word} from "./words";
  import { createEventDispatcher, beforeUpdate } from "svelte";
  const dispatch = createEventDispatcher();
  import { getRandomInt } from "./utils";

  export let word: word = "hello";
  export let showWord: boolean = true;
  let previousWord = word;
  let typed = "";

  $: {
    const displayWord = typeof word === 'string' ? word : word.name

    if (typed.toLowerCase() === displayWord.toLowerCase()) {
      dispatch("message", { success: true, word });
      typed = "";
    }
    if (typed.toLowerCase() === 'skip') {
      dispatch("message", { success: false, word });
      typed = "";
    }
  }

  function renderTyped(typed: string, correct: string): string {
    const correctSpelled = correct
      .split("")
      .map((letter, i) => {
        const typedLetter = typed.split("")[i];
        return typedLetter === letter ? letter : "";
      })
      .join("");

    return `${correctSpelled}|${correct.substr(correctSpelled.length)}`;
  }

  const returnSound = "./sounds/return.wav";
  const typingSounds = ["./sounds/click.wav", "./sounds/soft-click.wav"];

  async function onTyping(e) {
    if (["Enter", "Backspace"].includes(e.key)) {
      new Audio(returnSound).play();
      return;
    }

    const sound = new Audio(
      typingSounds[getRandomInt(0, typingSounds.length - 1)]
    );
    sound.playbackRate += 0.15;
    sound.currentTime = 0;
    sound.play().catch((err) => console.log(err));
  }
</script>

<div>
  <div>
    {#if typed.length && showWord}
      {renderTyped(typed, typeof word === 'string' ? word : word.name)}
    {:else}
      {showWord || word === 'start' ? word: 'Guess'}
    {/if}
  </div>
  <!-- svelte-ignore a11y-autofocus -->
  <input
    on:keydown={onTyping}
    autofocus
    type="text"
    placeholder={showWord ? (typeof word === 'string' ? word : word.name) : ""}
    bind:value={typed}
  />
</div>
