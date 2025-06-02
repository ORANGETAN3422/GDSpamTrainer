<script>
  import StartTestButton from "./lib/StartTestButton.svelte";
  import ResultsCalculator from "./lib/ResultDisplays/ResultsCalculator.svelte";
  import TimeSelector from "./lib/TimeSelector.svelte";
  import Timer from "./lib/Timer.svelte";
    import Metronome from "./lib/Metronome.svelte";

  export let testTime = 5; // in seconds
  export let testActive = false;
  export let timeLeft = 0;
  export let debouncing = false;
</script>

<main>
  <Timer bind:testActive bind:testTime bind:timeLeft bind:debouncing/>
  <StartTestButton bind:testActive bind:debouncing></StartTestButton>
  <ResultsCalculator bind:testActive bind:timeLeft bind:testTime bind:debouncing />

  <section class="right-section">
    <TimeSelector bind:testTime bind:testActive />
    <h2>Metronome</h2>
    <Metronome bind:testActive/>
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
    z-index: 100;
  }
</style>
