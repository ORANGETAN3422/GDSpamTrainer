<script>
  export let testActive;
  export let debouncing;
</script>

<h3>
  <b>{testActive ? "" : (debouncing ? "Please wait before starting again..." : "Start spamming [Space] to begin")}</b>
</h3>