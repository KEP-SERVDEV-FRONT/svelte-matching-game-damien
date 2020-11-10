<script>
export let 
isOpened: boolean, 
isSame: boolean, 
id: number;
</script>

<div 
  class="card card-id{id}" 
  class:is-opened={isOpened} 
  class:is-same={isSame}
  on:click
>
  <div class="card-inner">
    <span class="card-index"></span>
    <span class="card-content"></span>
  </div>
</div>

<style lang="scss" src="Card.scss"></style>