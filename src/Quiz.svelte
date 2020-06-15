<script>
    import { fade, blur, fly, slide, scale } from 'svelte/transition';
    import { onMount, onDestroy, beforeUpdate, afterUpdate } from 'svelte';
    import Question from './Question.svelte';

    let activeQuestion = 0;
    let score = 0;
    let quiz = getQuiz();

    onMount(() => {
        console.log('Component onMount!!!')
    })

    beforeUpdate(() => {
        console.log('Component beforeUpdate!!!')
    })

    afterUpdate(() => {
        console.log('Component afterUpdate!!!')
    })

    onDestroy(() => {
        console.log('Component onDestroy!!!')
    })

    async function getQuiz() {
        const res = await fetch('https://opentdb.com/api.php?amount=10&category=20&type=multiple');
        const quiz = await res.json();
        return quiz;
    }

    function nextQuestion() {
        activeQuestion = activeQuestion + 1;
    }

    function resetQuiz() {
        score = 0;
        activeQuestion = 0;
        quiz = getQuiz();
    }

    function addToScore() {
        score = score + 1;
    }

    $: if(score > 7) {
        console.log('YOU WON!!!!')
        resetQuiz();
    }

    // Reactive declaration
    $: questionNumber = activeQuestion + 1;
</script>

<div>
    <button on:click|once={resetQuiz}>Start New Quiz</button>
    <h3>My Score: {score}</h3>
    <h4>Question #{questionNumber}</h4>

    {#await quiz}
        Loading...
    {:then data}
        {#each data.results as question, index}
            {#if index === activeQuestion}
                <div in:fly={{ x: 100 }} out:fly={{ x: -200 }} class="fade-wrapper">
                    <Question {addToScore} {nextQuestion} {question} />
                </div>
            {/if}
        {/each}
    {/await}
</div>

<style>
    .fade-wrapper {
        position: absolute;
    }
</style>