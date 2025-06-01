<script>
  import StartTestButton from "./lib/StartTestButton.svelte";
  import ResultsCalculator from "./lib/ResultDisplays/ResultsCalculator.svelte";
  import TimeSelector from "./lib/TimeSelector.svelte";

  import { onMount } from "svelte";

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
    if (e.code !== "Space") return;

    if (!testActive) {
      debouncing = true;
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
          debouncing = false;
        }, debounceTime);
      }, testTime * 1000);

      updateElapsedInterval = setInterval(() => {
        timeLeft = testTime * 1000 - Math.floor(performance.now() - startTime);
        console.log(testTime);
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

<main>
  <StartTestButton bind:testActive bind:debouncing></StartTestButton>
  <ResultsCalculator bind:testActive bind:timeLeft bind:testTime />

  <section class="right-section">
    <TimeSelector bind:testTime bind:testActive />
</section>
</main>

<style>
   .right-section {
        right: 0;
    }

    section {
        position: absolute;
        top: 0;
        height: 100%;
        width: 23%;

        background-color: rgba(0, 0, 0, 0.15);
    }
</style>