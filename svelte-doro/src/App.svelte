<script>
  import { onDestroy } from "svelte";
  import { sec2time } from "./utils.js";
  import "bulma/css/bulma.css";

  // Presets

  let presets = [
    { name: "Work", seconds: 5, isRest: false },
    { name: "Long Work", seconds: 2700, isRest: false },
    { name: "Rest", seconds: 500, isRest: true },
    { name: "Long Rest", seconds: 1800, isRest: true }
  ];

  // Default preset is index-0, which is work right now
  let selected = presets[0];

  let isStarted = false;

  // Keep track of completed Pomodoros
  let completedPomodoros = 0; // should load from offline

  // The computed display based on seconds
  $: display = sec2time(seconds);

  // The selected preset's seconds
  $: seconds = selected.seconds;

  // isRest is based on the selected preset
  $: isRest = selected.isRest;

  function handleReset() {
    isStarted = false;
    seconds = selected.seconds;
  }

  const interval = setInterval(() => {
    if (isStarted && seconds > 0) {
      console.log(seconds);
      seconds -= 1;
      if (seconds == 0) {
        isStarted = false;
        if (!isRest) {
          // This wasn't rest; increasee pomodoro
          completedPomodoros += 1;
        }
      }
    }
  }, 1000);

  // Clean up
  onDestroy(() => clearInterval(interval));
</script>

<style>
  :global(body) {
    display: flex;
    height: 100%;
    width: 100%;
    align-items: center;
    justify-content: center;
  }
</style>

<div class="container is-fluid" style="max-width: 450px;">
  <div class="content">
    <p class="is-small has-text-weight-light">What is Pomodoro?</p>
  </div>
  <div class="box" style="padding: 2.20rem;">
    <div class="columns ">
      <div class="column">
        <div class="level">
          <div class="level-item">
            <progress
              class="progress is-small {isStarted ? 'is-success' : ''}"
              value={seconds}
              max={selected.seconds}>
              {(seconds / selected.seconds) * 100}
            </progress>
          </div>
        </div>
        <div class="level">
          <div class="level-item content">
            <p class="subtitle is-2 is-family-monospace">{display}</p>
          </div>
        </div>
        <div class="level">
          <div class="level-item content">
            <span
              class="tag is-medium {completedPomodoros > 0 ? 'is-success' : 'is-light'}">
              🍅 {completedPomodoros}
            </span>
          </div>
        </div>
        <div class="level">
          <div class="level-left">
            <div class="level-item">
              <div class="field">
                <div class="control">
                  <div class="select">
                    <select bind:value={selected}>
                      {#each presets as preset}
                        <option value={preset}>{preset.name}</option>
                      {/each}
                    </select>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="level-right">
            <div class="level-item">
              <div class="field is-grouped">
                <p class="control">
                  <button
                    disabled={seconds === 0 ? 'disabled' : ''}
                    class="button {isStarted ? 'is-danger' : 'is-success'}"
                    on:click={() => (isStarted = !isStarted)}>
                    {isStarted ? 'Stop' : 'Start'}
                  </button>
                </p>
                <p class="control">
                  <button class="button" on:click={handleReset}>Reset</button>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</div>
