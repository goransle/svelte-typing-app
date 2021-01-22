
<script lang="ts">
    import {createEventDispatcher} from "svelte";
    export let words = []
</script>

<div>
	{#if words.length}
		<ul>
			{#each words as word}
				<li>{word}</li>
			{/each}
        </ul>
	{/if}
</div>