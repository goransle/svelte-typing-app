<script lang="ts">
import type { word } from './words';
  export let words: word[] = [];

  $: if (words) {
    const list = document.getElementById('wordList');
    if (list) {
      list.scrollTop = list.scrollHeight;
    }
  }
</script>

<div id="wordList">
  {#if words.length}
    <ul>
      {#each words as word}
        {#if typeof word === 'string'}
          <li>{word}</li>
        {:else}
          <li>{word.name}{word.typed === false ? "(skipped)" : ""}</li>
        {/if}
      {/each}
    </ul>
  {/if}
</div>

<style>
  ul {
    max-height: 20vh;
    overflow-y: scroll;
    list-style: none;
    padding: 0;
  }

  li {
    padding: 0;
  }
  div {
    border: 1px solid black;
    margin: 0 auto;
    width: 15em;
  }
</style>
