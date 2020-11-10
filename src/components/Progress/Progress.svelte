<script>
export let 
progressPercent: number,
remainSeconds: number,
isComplete: boolean,
isStarted: boolean, 
isWarning: boolean, 
isDanger: boolean, 
isDone: boolean;
</script>

<div class="progress"
  class:is-start={isStarted}
  class:is-done={isDone}
  class:warning={isWarning}
  class:danger={isDanger && !isDone}
>
  <div class="progress-bar" style="width:{progressPercent}%"></div>
  <span class="progress-bar-light"></span>
  <strong class="time">
    {#if isStarted && !isDone && !isComplete}
      {#if isDanger}
      <span class="time-info shake">{Math.ceil(remainSeconds)}</span>
      {/if}
    {:else if isDone && !isComplete}
      <span class="time-info">GAME OVER</span>
    {:else if isDone && isComplete}
      <span class="time-info">COMPLETE!</span>
    {:else}
      <span class="time-info">READY</span>
    {/if}
  </strong>
</div>

<style lang="scss" src="Progress.scss"></style>