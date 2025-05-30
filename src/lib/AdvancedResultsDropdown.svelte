<script>
    let collapsed = true;
    export let clickIntervals;

    function ToggleView() {
        collapsed = !collapsed;
    }

    // Result Calculations
    function calculateMean(data) {
        let sum = 0;
        for (let i = 0; i < data.length; i++) {
            sum += data[i];
        }
        return Math.round(sum / data.length) + " ms";
    }

    function calculateMedian(data) {
        data = data.slice().sort((x, y) => x - y);
        return Math.round(data[Math.round((data.length - 1) / 2)]) + " ms";
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
        return currentStreak > bestStreak ? currentElem : bestElem + " ms";
    }

    function fetchLongest(data) {
        data = data.slice().sort((x, y) => x - y);
        return data[data.length - 1] + " ms";
    }

    function fetchShortest(data) {
        data = data.slice().sort((x, y) => x - y);
        return data[0] + " ms";
    }
</script>

<link
    rel="stylesheet"
    href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=arrow_drop_down"
/>

<main class={[{ collapsed }]}>
    <p>
        <button onclick={ToggleView}>
            <span class={["material-symbols-outlined", { collapsed }]}>
                arrow_drop_down
            </span>
        </button>
    </p>

    {#if !collapsed}
        <p>
            Mean Time Difference: {calculateMean(clickIntervals)}
            <br />
            Median Time Difference: {calculateMedian(clickIntervals)}
            <br />
            Mode Time Difference: {calculateMode(clickIntervals)}
            <br />
            <br />
            Largest Time Difference: {fetchLongest(clickIntervals)}
            <br />
            Shortest Time Difference: {fetchShortest(clickIntervals)}
        </p>
    {/if}
</main>

<style>
    button {
        background-color: rgba(0, 0, 0, 0);
        border: hidden;
        position: absolute;

        top: -13px;
        left: 0;
        width: 100%;
        rotate: 0deg;
    }

    span {
        transform: scaleY(-1);
        transition: 0.3s all ease-in-out;
    }

    button .collapsed {
        transform: scaleY(1);
    }
    
    main {
        height: auto;
        border: 1px #444444 solid;
        border-radius: 10px;
        background-color: #242424;

        position: relative;
        overflow: hidden;
    }
</style>
