<script>
  import { onMount } from "svelte";
  import ResultsCalculator from "./ResultsCalculator.svelte";

  let startTime = 0;
  let debounceTime = 1 * 1000;
  let debouncing = false;
  export let testTime = 5; // in seconds
  export let testActive = false;
  export let timeLeft = 0;

  let testEndTimeout = null;
  let updateElapsedInterval = null;
  let debounceTimeout = null;

  function handleKeydown(e) {
    if (debouncing) return;
    if (e.code !== 'Space') return;

    if (!testActive) {
      debouncing = true
      testActive = true;
      startTime = performance.now();
      timeLeft = testTime * 1000;

      // Clear existing timeout if it exists
      if (testEndTimeout) clearTimeout(testEndTimeout);
      if (updateElapsedInterval) clearInterval(updateElapsedInterval);
      if (debounceTimeout) clearInterval(debounceTimeout);

      testEndTimeout = setTimeout(() => {
        testActive = false;
        testEndTimeout = null;
        clearInterval(updateElapsedInterval);

        debounceTimeout = setTimeout(() => {
          debouncing = false
        }, debounceTime);
      }, testTime * 1000);

      updateElapsedInterval = setInterval(() => {
        timeLeft = testTime * 1000 - Math.floor(performance.now() - startTime);
        console.log(testTime)
      }, 6); // about 167 updates per second
    }
  }

  onMount(() => {
    document.addEventListener("keydown", handleKeydown);

    return () => {
      document.removeEventListener("keydown", handleKeydown);
      if (testEndTimeout) clearTimeout(testEndTimeout);
      if (updateElapsedInterval) clearInterval(updateElapsedInterval);
      if (debounceTimeout) clearTimeout(debounceTimeout);
      if (!testActive) timeLeft = 0;
    };
  });
</script>

<h3>
  <b>{testActive ? "In progress..." : (debouncing ? "Please Wait..." : "Start spamming [Space] to being")}</b>
</h3>

<ResultsCalculator bind:testActive bind:timeLeft bind:testTime></ResultsCalculator>
