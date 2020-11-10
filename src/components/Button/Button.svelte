<script>
export let 
buttonClassName: string,
isStarted: boolean;
</script>

<button type="button" 
  class="btn {buttonClassName}" 
  disabled={isStarted} 
  on:click
>
  <slot></slot>
</button>

<style lang="scss" src="Button.scss"></style>