<script>
    import { onMount } from "svelte";
    import ResultsGraph from "./ResultsGraph.svelte";
    import AdvancedResultsDropdown from "./AdvancedResultsDropdown.svelte";

    export let testActive;
    export let timeLeft;
    export let testTime;
    let timeElapsed;
    let currTestTime;

    let clickTimes = [];
    let clickIntervals = [];

    function formatMilliseconds(ms) {
        let minutes = Math.floor(ms / 60000);
        let seconds = ((ms % 60000) / 1000).toFixed(0);

        return minutes === 0
            ? seconds.toString() + "s"
            : `${minutes}:${parseInt(seconds) < 10 ? "0" : ""}${seconds}`;
    }

    function handleKeydown(e) {
        if (e.code !== "Space") return;
        if (!testActive) return;

        timeElapsed = testTime * 1000 - timeLeft;

        clickTimes.push(timeElapsed);

        if (clickTimes.length > 1) {
            // Calculate the time difference between the last two clicks
            let timeDifference =
                clickTimes[clickTimes.length - 1] -
                clickTimes[clickTimes.length - 2];
            clickIntervals = [...clickIntervals, timeDifference];
        }
    }

    onMount(() => {
        document.addEventListener("keydown", handleKeydown);
        return () => {
            document.removeEventListener("keydown", handleKeydown);
        };
    });

    $: if (testActive) {
        currTestTime = testTime
        clickTimes = [];
        clickIntervals = [];
        timeElapsed = 0;
    }
</script>

<main>
    <p>
        {testActive ? formatMilliseconds(timeLeft) + " left" : " "}
    </p>

    <br />

    {#if clickIntervals}
        <ResultsGraph {clickIntervals} />
    {/if}
</main>

<section class="left-section">
    <h2>Statistics</h2>
    <div class="general-wrapper">
        Test time: {formatMilliseconds(testTime * 1000)}
        <br />
        CPS: {!testActive ? clickIntervals.length / currTestTime : "0"}
    </div>
    <div class="dropdown-wrapper">
        <b>More Stats</b>
        <AdvancedResultsDropdown bind:clickIntervals />
    </div>
</section>

<section class="right-section">
    <div class="time-button-con">
        <button class="time-button" onclick={() => (testTime = 1)}>
            1 Second</button
        >
        <button class="time-button" onclick={() => (testTime = 5)}>
            5 Seconds</button
        >
        <button class="time-button" onclick={() => (testTime = 10)}>
            10 Seconds</button
        >
         <button class="time-button" onclick={() => (testTime = 30)}>
            30 Seconds</button
        >
         <button class="time-button" onclick={() => (testTime = 60)}>
            1 minute</button
        >
        <button class="time-button" onclick={() => (testTime = 120)}>
            2 minutes</button
        >
    </div>
</section>

<style>
    .dropdown-wrapper {
        top: 10px;
        left: 2%;
        width: 96%;
        position: inherit;
        margin-top: 400px;
    }

    div {
        width: 100%;
    }

    h2 {
        text-decoration: underline;
    }

    section {
        position: absolute;
        top: 0;
        height: 100%;
        width: 23%;

        background-color: rgba(0, 0, 0, 0.15);
    }

    .left-section {
        left: 0;
    }

    .right-section {
        right: 0;
    }

    .time-button {
        width: 100%;
        height: 50px;
        box-shadow: 0 0 5px black;
        font-size: 0.8em;
    }

    .time-button-con {
        padding: 10px;
        width: 90%;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 10px;
    }
</style>
