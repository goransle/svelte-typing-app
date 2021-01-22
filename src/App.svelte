<script lang="ts">
	import InputBox from "./InputBox.svelte"
	import WordList from "./WordList.svelte"

	import {getRandomInt} from './utils';
	
	let words = [
		"start"
	];

	async function fetchWords(){
		const response = await fetch("https://api.datamuse.com/words?ml=ringing+in+the+ears");

		const result = await response.json()
		words = Object.values(result).map((obj: any) =>{
			if(obj.word)
				return obj.word;
		})
			

	}

	function getRandomWord(){
		return words[getRandomInt(0, words.length - 1)]
	}


	let currentWord = getRandomWord()
	let typedWords: string[] = [];
	
	function handleMessage(e: any){
		console.log(e.detail)
			const { 
				detail: {
					word,
					success
				} 
			} = e;
			if(success && word){
				console.log(word);
				
				typedWords.push(word);
				typedWords = typedWords;

				currentWord= getRandomWord();
			}

	}

</script>

<main>
	{#await fetchWords()}
	<p>...waiting for more words</p>
	{/await}
	<WordList words={typedWords} />
	<InputBox word={currentWord} on:message={handleMessage}/>
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