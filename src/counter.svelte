<script>
  import { onDestroy } from 'svelte';

  export let isActive = false;

  let elapsedTime = 0;
  let interval;
  let startTime;

  // Watch for changes in isActive
  $: if (isActive) {
    if (!interval) {
      startTime = Date.now() - elapsedTime;
      interval = setInterval(() => {
        elapsedTime = Date.now() - startTime;
      }, 100);
    }
  } else {
    clearInterval(interval);
    interval = null;
  }

  onDestroy(() => {
    clearInterval(interval);
  });
</script>

<p>Time Elapsed: {(elapsedTime / 1000).toFixed(2)} seconds</p>
