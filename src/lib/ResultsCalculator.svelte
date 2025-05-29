<script>
    import { onMount } from "svelte";
    import ResultsGraph from "./ResultsGraph.svelte";

    export let testActive;
    export let timeLeft;
    export let testTime;
    let timeElapsed;

    let clickTimes = [];
    let clickIntervals = [];

    function formatMilliseconds(ms) {
        if (!testActive) return "";

        let minutes = Math.floor(ms / 60000);
        let seconds = ((ms % 60000) / 1000).toFixed(0);

        return minutes === 0
            ? seconds.toString()
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
            clickIntervals.push(timeDifference);
        }
    }

    onMount(() => {
        document.addEventListener("keydown", handleKeydown);
        return () => {
            document.removeEventListener("keydown", handleKeydown);
        };
    });

    $: if (testActive) {
        clickTimes = [];
        clickIntervals = [];
        timeElapsed = 0;
    }

    // Result Calculations

    function calculateMean(data) {
        let sum = 0;
        for (let i = 0; i < data.length; i++) {
            sum += data[i];
        }
        return Math.round(sum / data.length);
    }

    function calculateMedian(data) {
        data = data.slice().sort((x, y) => x - y);
        return Math.round(data[Math.round((data.length - 1) / 2)]);
    }

    function calculateMode(data) {
        data = data.slice().sort((x, y) => x - y);

        var bestStreak = 1;
        var bestElem = data[0];
        var currentStreak = 1;
        var currentElem = data[0];

        for (let i = 1; i < data.length; i++) {
            if (data[i - 1] !== data[i]) {
                if (currentStreak > bestStreak) {
                    bestStreak = currentStreak;
                    bestElem = Math.round(currentElem);
                }
                currentStreak = 0;
                currentElem = data[i];
            }
            currentStreak++;
        }
        return currentStreak > bestStreak ? currentElem : bestElem;
    }

    function fetchLongest(data) {
        data = data.slice().sort((x, y) => x - y);
        return data[0];
    }

    function fetchShortest(data) {
        data = data.slice().sort((x, y) => x - y);
        return data[data.length - 1];
    }
</script>

<p>
    {formatMilliseconds(timeLeft)}
</p>

<br />

{#if !testActive}
    <p>
        Mean Time Difference: {calculateMean(clickIntervals)} ms
        <br />
        Median Time Difference: {calculateMedian(clickIntervals)} ms
        <br />
        Mode Time Difference: {calculateMode(clickIntervals)} ms
        <br />
        <br />
        Largest Time Difference: {fetchLongest(clickIntervals)} ms
        <br />
        Shortest Time Differene: {fetchShortest(clickIntervals)} ms
    </p>

    <ResultsGraph bind:clickIntervals></ResultsGraph>
{/if}
