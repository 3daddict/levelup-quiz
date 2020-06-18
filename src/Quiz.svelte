<script>
    import { fade, blur, fly, slide, scale } from 'svelte/transition';
    import { onMount, onDestroy, beforeUpdate, afterUpdate } from 'svelte';
    import Question from './Question.svelte';
    import Modal from './Modal.svelte';
    import { score } from './store.js';

    let activeQuestion = 0;
    let quiz = getQuiz();
    let isModalOpen = false;

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
        isModalOpen = false;
        score.set(0);
        activeQuestion = 0;
        quiz = getQuiz();
    }

    $: if($score > 7) {
        isModalOpen = true;
    }

    // Reactive declaration
    $: questionNumber = activeQuestion + 1;
</script>

<div>
    <button on:click|once={resetQuiz}>Start New Quiz</button>
    <h3>My Score: {$score}</h3>
    <h4>Question #{questionNumber}</h4>

    <div class="container">
        {#await quiz}
            Loading...
        {:then data}
            {#each data.results as question, index}
                {#if index === activeQuestion}
                    <div class="fade-wrapper">
                        <Question {nextQuestion} {question} />
                    </div>
                {/if}
            {/each}
        {/await}
    </div>
    
</div>

{#if isModalOpen}
    <Modal on:close={resetQuiz}>
        <h2>You Won!</h2>
        <p>Congrats!!!</p>
        <button on:click={resetQuiz}>Start Over</button>
    </Modal>
{/if}

<style>
    .fade-wrapper {
        position: absolute;
    }
    .container {
        min-height: 500px;
    }
</style>