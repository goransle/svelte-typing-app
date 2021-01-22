<script lang="ts">
    import {createEventDispatcher, beforeUpdate} from "svelte";
    const dispatch = createEventDispatcher();
    import {getRandomInt} from './utils';

    export let word: string = 'hello';
    let previousWord = word;
    let typed = '';

    $: {
        if(typed.toLowerCase() === word.toLowerCase()){
            dispatch("message", {success: true, word})
            typed = ""
        }
    }

    function renderTyped(typed: string, correct: string): string{
        const correctSpelled = correct.split('').map((letter, i) =>{
            const typedLetter = typed.split('')[i];
            return  typedLetter === letter ? letter : ''; 
        }).join('')

        return `${correctSpelled}|${correct.substr(correctSpelled.length)}` 
    }

    const returnSound = './sounds/return.wav';
    const typingSounds = [
        './sounds/click.wav',
        './sounds/soft-click.wav'
    ];


   async function onTyping(e){
        if(['Enter', 'Backspace'].includes(e.key)){        
            new Audio(returnSound).play();
            return;
        }
        console.log(e.key);
        

        const sound = new Audio(typingSounds[getRandomInt(0, typingSounds.length - 1)])
        sound.playbackRate += .15;
        sound.currentTime = 0;
        sound.play().catch(err => console.log(err));
    }
</script>

<div>
    <div>
        {#if typed.length}
            {renderTyped(typed, word)}
        {:else}
            {word}
        {/if}
    </div>
    <!-- svelte-ignore a11y-autofocus -->
    <input on:keydown={onTyping} autofocus type="text" placeholder={word} bind:value={typed} />
</div>