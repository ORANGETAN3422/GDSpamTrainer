<script>
    import { onMount } from "svelte";
    import ResultDisplay from "./ResultDisplay.svelte";

    export let testActive;
    export let timeLeft;
    export let testTime;
    export let debouncing;
    let currTestTime;

    let clickTimes = [];
    let clickIntervals = [];
    let releaseTimes = [];
    let keyReleased = true;

    function handleKeydown(e) {
        if (e.code !== "Space") return;
        if (!testActive) return;
        if (!keyReleased) return;

        clickTimes.push(performance.now());
        keyReleased = false;

        if (clickTimes.length > 1) {
            let timeDifference =
                clickTimes[clickTimes.length - 1] -
                clickTimes[clickTimes.length - 2];
            clickIntervals = [...clickIntervals, timeDifference];
        }
    }

    function handleKeyup(e) {
        if (e.code !== "Space") return;
        if (!testActive) return;

        keyReleased = true;
        releaseTimes.push(performance.now()); // <-- record release timestamp
    }

    onMount(() => {
        document.addEventListener("keydown", handleKeydown);
        document.addEventListener("keyup", handleKeyup);
        return () => {
            document.removeEventListener("keydown", handleKeydown);
            document.removeEventListener("keyup", handleKeyup); // remove this too
        };
    });

    $: if (testActive) {
        currTestTime = testTime;
        clickTimes = [];
        clickIntervals = [];
        releaseTimes = [];
        keyReleased = true;
    }
</script>

<ResultDisplay 
    bind:timeLeft 
    bind:testTime 
    bind:currTestTime 
    bind:clickIntervals 
    bind:testActive 
    bind:debouncing
    bind:releaseTimes
    bind:clickTimes
/>
