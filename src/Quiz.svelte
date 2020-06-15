<script>
    import Question from './Question.svelte';
    let quiz = getQuiz();

    async function getQuiz() {
        const res = await fetch('https://opentdb.com/api.php?amount=10&category=20&type=multiple');
        const quiz = await res.json();
        return quiz;
    }

    function handleClick() {
        quiz = getQuiz();
    }
</script>

<div>
    <button on:click={handleClick}>Get Questions</button>

    {#await quiz}
        Loading...
    {:then data}
        {#each data.results as question}
            <Question {question} />
        {/each}
    {/await}
</div>

<style>

</style>