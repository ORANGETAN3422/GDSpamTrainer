<script>
    import { onDestroy } from "svelte";
    export let testActive;

    let metronomeOn = false;

    let metronomeSpeed = 10; // CPS
    let context;
    let buffer;
    let timer;

    let currentSound = "metronome.mp3";
    let affectPitch = false;

    async function initAudio() {
        if (!context) {
            context = new window.AudioContext();
        }
        if (context.state === "suspended") {
            await context.resume();
        }

        const soundPath = `${import.meta.env.BASE_URL}/sounds/${currentSound}`; // don't include "public/"
        const res = await fetch(soundPath);
        const arrayBuffer = await res.arrayBuffer();
        buffer = await context.decodeAudioData(arrayBuffer);
    }

    function playSound() {
        if (!buffer || !context) return;
        const source = context.createBufferSource();
        source.buffer = buffer;
        source.connect(context.destination);

        if (affectPitch) {
            const randomPitch = 0.85 + Math.random() * 0.3;
            source.playbackRate.value = randomPitch;
        }

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
    $: if (testActive && metronomeOn) {
        initAudio().then(startRepeating);
    } else {
        stopRepeating();
    }

    // React to sound change WHILE playing
    $: if (testActive && currentSound && metronomeOn) {
        initAudio();
    }

    onDestroy(stopRepeating);
</script>

{#if metronomeOn}
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
            <button
                on:click={() => {
                    currentSound = "metronome.mp3";
                    affectPitch = false;
                }}>Metronome</button
            >
            <button
                on:click={() => {
                    currentSound = "mouse.ogg";
                    affectPitch = false;
                }}>Mouse Click</button
            >
            <button
                on:click={() => {
                    currentSound = "keyboard.ogg";
                    affectPitch = true;
                }}>Keyboard Click</button
            >
        </div>
        <br />
        <button
            on:click={() => {
                metronomeOn = false;
            }}
        >
            Turn off metronone
        </button>
    </div>
{:else}
    <button
        on:click={() => {
            metronomeOn = true;
        }}
    >
        Turn on metronome
    </button>
{/if}
