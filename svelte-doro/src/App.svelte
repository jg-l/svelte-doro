<script>
  import { onDestroy } from "svelte";

  let seconds = 600;
  let started = false;

  $: display = sec2time(seconds);

  function toPretty(s) {
    var a = new Date(s);
    return a
      .toISOString()
      .substr(11, 8)
      .toString();
  }
  function sec2time(timeInSeconds) {
    var pad = function(num, size) {
        return ("000" + num).slice(size * -1);
      },
      time = parseFloat(timeInSeconds).toFixed(3),
      hours = Math.floor(time / 60 / 60),
      minutes = Math.floor(time / 60) % 60,
      seconds = Math.floor(time - minutes * 60),
      milliseconds = time.slice(-3);

    return pad(hours, 2) + ":" + pad(minutes, 2) + ":" + pad(seconds, 2);
  }

  const interval = setInterval(() => {
    if (started) {
      seconds -= 1;
      display = toPretty(seconds);
      display = display;
    }
  }, 1000);

  onDestroy(() => clearInterval(interval));
</script>

<h1>Hey!</h1>
<p>{seconds}</p>
<p>{display}</p>
<button on:click={() => (started = !started)}>
  {started ? 'Stop' : 'Start'}
</button>
<button on:click={() => (seconds = 0)}>Reset</button>
