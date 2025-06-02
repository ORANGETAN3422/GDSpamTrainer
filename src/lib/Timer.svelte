<script>
  import { onMount } from "svelte";

  let startTime = 0;
  let debounceTime = 1 * 1000;
  export let debouncing;
  export let testTime; // in seconds
  export let testActive;
  export let timeLeft;

  let testEndTimeout = null;
  let updateElapsedInterval = null;
  let debounceTimeout = null;

  function onTestEnd() {
    testActive = false;
    testEndTimeout = null;
    clearInterval(updateElapsedInterval);

    debounceTimeout = setTimeout(() => {
      debouncing = false;
    }, debounceTime);
  }

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

      testEndTimeout = setTimeout(() => onTestEnd(), testTime * 1000);

      updateElapsedInterval = setInterval(() => {
        timeLeft = testTime * 1000 - Math.floor(performance.now() - startTime);
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

  $: if (!testActive) {
    onTestEnd()
  }
</script>
