<script>
    export let testActive;
    export let debouncing;

    function stopTest() {
        testActive = false;
        debouncing = false;
    }
</script>

<button onclick={() => stopTest()} > Stop Test </button>