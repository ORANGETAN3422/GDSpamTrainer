<script>
    import { onDestroy } from "svelte";

    export let testActive;

    let metronomeSound = new Audio("metronome.mp3");
    let metronomeSpeed = 10; // In CPS

    let timer;

    function playSound() {
        metronomeSound.currentTime = 0;
        metronomeSound.play();
    }

    function stopRepeating() {
        clearInterval(timer);
    }

    $: if (testActive) {
        stopRepeating();
        timer = setInterval(() => {
            playSound();
        }, 1000 / metronomeSpeed);
    }

    $: if (!testActive) {
        stopRepeating();
    }

    // Clean up on component destroy
    onDestroy(stopRepeating);
</script>

<div class="space-y-4">
    <label for="interval">
        {metronomeSpeed} CPS / {metronomeSpeed * 60} BPM
        <br />
    </label>
    <input
        id="interval"
        type="range"
        min="1"
        max="10"
        step="1"
        bind:value={metronomeSpeed}
    />
</div>
