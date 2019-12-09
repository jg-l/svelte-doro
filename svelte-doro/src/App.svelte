<script>
  import { onDestroy } from "svelte";
  let elapsed = 0;
  let timer = 5000;
  let last_time = window.performance.now();
  let frame;
  let started = false;
  let selected;
  let answer = "";

  let presets = [
    { id: 1, text: `Work`, duration: 5000 },
    { id: 2, text: `Rest`, duration: 5000 }
  ];

  (function update() {
    frame = requestAnimationFrame(update);
    const time = window.performance.now();
    // console.log(last_time, time);
    // console.log(duration - elapsed);

    if (timer === 0) {
      started = false;
    }

    if (timer !== 0 && started) {
      console.log("ping");
      timer -= Math.min(time - last_time, timer - elapsed);
    }

    console.log(timer);
    last_time = time;
  })();
  onDestroy(() => {
    cancelAnimationFrame(frame);
  });
</script>

<label>
  elapsed time:
  <progress value={timer} />
</label>

<div>{(timer / 1000).toFixed(1)}s</div>
<label>started: {started}</label>

<select bind:value={selected} on:change={() => (answer = '')}>
  {#each presets as preset}
    <option value={preset}>{preset.text}</option>
  {/each}
</select>

<button on:click={() => (started = !started)}>Start</button>
<button on:click={() => (timer = 5000)}>Reset</button>
