<script>
  import { onDestroy } from "svelte";
  export let testActive;

  let metronomeSpeed = 10; // CPS
  let context;
  let buffer;
  let timer;

  let currentSound = "metronome.mp3";

  async function initAudio() {
    if (!context) {
      context = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (context.state === "suspended") {
      await context.resume();
    }

    const soundPath = `public/sounds/${currentSound}`; // don't include "public/"
    const res = await fetch(soundPath);
    const arrayBuffer = await res.arrayBuffer();
    buffer = await context.decodeAudioData(arrayBuffer);
  }

  function playSound() {
    if (!buffer || !context) return;
    const source = context.createBufferSource();
    source.buffer = buffer;
    source.connect(context.destination);
    source.start();
  }

  function startRepeating() {
    stopRepeating();
    timer = setInterval(() => {
      playSound();
    }, 1000 / metronomeSpeed);
  }

  function stopRepeating() {
    clearInterval(timer);
  }

  // React to testActive changes
  $: if (testActive) {
    initAudio().then(startRepeating);
  } else {
    stopRepeating();
  }

  // React to sound change WHILE playing
  $: if (testActive && currentSound) {
    initAudio();
  }

  onDestroy(stopRepeating);
</script>

<div>
  <label for="interval">
    {metronomeSpeed} CPS / {metronomeSpeed * 60} BPM
    <br />
  </label>
  <input
    id="interval"
    type="range"
    min="1"
    max="20"
    step="1"
    bind:value={metronomeSpeed}
  />

  <div class="sound-selection-buttons">
    <button on:click={() => (currentSound = "metronome.mp3")}>Metronome</button>
    <button on:click={() => (currentSound = "mouse.ogg")}>Mouse Click</button>
    <button on:click={() => (currentSound = "keyboard.ogg")}>Keyboard Click</button>
  </div>
</div>
