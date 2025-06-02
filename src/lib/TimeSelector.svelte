<script>
    export let testTime;
    export let testActive;

    function changeTestTime(time) {
        if (testActive) return;
        testTime = time
    }
</script>

<div class="time-button-con">
    <button class="time-button" onclick={() => changeTestTime(1)}>
        1 Second</button
    >
    <button class="time-button" onclick={() => changeTestTime(5)}>
        5 Seconds</button
    >
    <button class="time-button" onclick={() => changeTestTime(10)}>
        10 Seconds</button
    >
    <button class="time-button" onclick={() => changeTestTime(30)}>
        30 Seconds</button
    >
    <button class="time-button" onclick={() => changeTestTime(60)}>
        1 minute</button
    >
    <button class="time-button" onclick={() => changeTestTime(120)}>
        2 minutes</button
    >
</div>

<style>
    .time-button {
        width: 100%;
        height: 50px;
        font-size: 0.8em;

        border: 1px #444444 solid;
        border-radius: 10px;
        background-color: #242424;
    }

    .time-button-con {
        padding: 10px;
        width: 90%;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 10px;
    }
</style>