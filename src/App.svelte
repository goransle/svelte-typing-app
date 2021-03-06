<script lang="ts">
  import type { word } from "./words";
  import InputBox from "./InputBox.svelte";
  import WordList from "./WordList.svelte";
  import Map from "./Map.svelte";

  import * as MapJSON from "@highcharts/map-collection/custom/europe.geo.json";

  import { getRandomInt } from "./utils";

  let params = new URLSearchParams(window.location.search);

  let words: Array<word> = ["start"];
  let mode: "maps" | "words" | "kill" = params.get("mode") as any || "maps";

  async function fetchWords() {
    const response = await fetch(
      "https://api.datamuse.com/words?ml=ringing+in+the+ears"
    );

    const result = await response.json();
    words = Object.values(result).map((obj: any) => {
      if (obj.word) return obj.word;
    });
  }

  async function setupMapTyping() {
    const { features: countries } = MapJSON;

    const countryMap = countries.map((country) => {
      return {
        name: country.properties.name,
        key: country.properties["hc-key"],
        typed: false,
      };
    });
    words.push(...countryMap);
  }

  function getRandomWord(): word {
    return words[getRandomInt(0, words.length - 1)];
  }

  function getRandomUntypedWord(): word {
    while (true) {
      const word = getRandomWord();
      if (!typedWords.find((typedWord) => typedWord === word)) {
        return word;
      }
    }
  }

  let currentWord = getRandomWord();
  let typedWords: word[] = [];

  function handleMessage(e: {
    detail: {
      word: word;
      success: boolean;
    };
  }) {
    console.log(e.detail);
    const {
      detail: { word, success },
    } = e;
    if (success && word) {
      if (typeof word === "object") {
        word.typed = true;
      }
      typedWords.push(word);
      typedWords = typedWords;

      currentWord = getRandomUntypedWord();
    }
    if (success === false && word) {
      typedWords.push(word);
      typedWords = typedWords;

      currentWord = getRandomUntypedWord();
    }

    if(typedWords.length === words.length){
      mode = "kill"
    }
  }
</script>

<main>


  {#if mode === "words"}
    {#await fetchWords()}
      <p>...waiting for more words</p>
    {/await}
    <WordList words={typedWords} />
    <InputBox word={currentWord} on:message={handleMessage} />
  {/if}

  {#if mode === "maps"}
    {#await setupMapTyping()}
      <p>...waiting for more words</p>
    {/await}
    <WordList words={typedWords} />
    <Map
      words={typedWords}
      currentCountry={typeof currentWord !== "string" ? currentWord : undefined}
    />
    <InputBox showWord={false} word={currentWord} on:message={handleMessage} />
  {/if}
  {#if mode === "kill"}
    <h2>You did it!</h2>
    <a href="./">Restart?</a>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
