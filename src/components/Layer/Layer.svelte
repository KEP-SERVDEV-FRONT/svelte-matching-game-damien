<script>
import { customCardAmount, customSec, customBonusSec} from '@/store/stores';
import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();
import Button from '@/components/Button/Button.svelte';

enum LayerType {
  READY,
  START,
  GUIDE,
  BLOCK,
  CUSTOM
}

export let 
layerClassName = '',
gameMode: string,
layerType: number,
isDone: boolean, 
isStarted: boolean, 
isVisible: boolean,
isComplete: boolean,
isCustomMode: boolean;
</script>

<div class="layer {layerClassName}"
  class:layer-type-deemed={layerType === LayerType.START}
  class:layer-type-guide={layerType === LayerType.GUIDE}
  class:is-custom={isCustomMode}
>
  {#if layerType !== LayerType.BLOCK}
  <div class="layer-inner">
  {#if layerType === LayerType.READY}
    {#if !isStarted && !isDone && !isComplete}
    <div class="frame-intro"></div>
    <strong class="layer-title">프렌즈 카드 짝맞추기</strong>
    {/if}
    {#if isStarted && isDone && !isComplete}
    <div class="layer-head">
      <div class="frame-bg"></div>
      <strong class="head-title">미션 실패 ㅠㅠ</strong>
      <p class="head-txt">
        아쉽지만 재도전 해볼까요?
      </p>
    </div>
    {:else if isStarted && isDone && isComplete}
    <div class="layer-head is-complete">
      <div class="frame-bg"></div>
      <strong class="head-title">
        {gameMode} MODE CLEAR!
      </strong>
      <p class="head-txt">
        대단해요! 한번 더 할까요?
      </p>
    </div>
    {/if}
    <div class="layer-body">
      {#if !isStarted || isDone}
      <div class="box-controls">
        <Button buttonClassName={'btn-easy'} on:click={() => dispatch('easyModeStart')}>
          EASY
        </Button>
        <Button buttonClassName={'btn-normal'} on:click={() => dispatch('normalModeStart')}>
          NORMAL
        </Button>
        <Button buttonClassName={'btn-hard'} on:click={() => dispatch('hardModeStart')}>
          HARD
        </Button>
        <Button buttonClassName={'btn-custom'} on:click={() => dispatch('eventCustomVisible')}>
          CUSTOM
        </Button>
      </div>
      <Button buttonClassName={'btn-info'} on:click={() => dispatch('eventGideVisible')}>
        게임 설명
      </Button>
      {/if}
    </div>
  {:else if layerType === LayerType.START}
    <div class="layer-body">
      {#if isVisible}
      <strong class="title ready">READY</strong>      
      <strong class="title start">START!</strong>
      {/if}
    </div>
  {:else if layerType === LayerType.CUSTOM}
    <div class="layer-body">
      <fieldset class="layer-field">
        <legend class="screen-out">커스텀 게임 셋팅</legend>
        <div class="layer-field-wrap">
          <div class="layer-field-item">
            <strong>게임 시간</strong>
            <select title="게임 시간" bind:value={$customSec}>
              <option value="10">10초</option>
              <option value="20">20초</option>
              <option value="30">30초</option>
              <option value="40">40초</option>
              <option value="50">50초</option>
              <option value="60">60초</option>
              <option value="90">90초</option>
            </select>
          </div>
          <div class="layer-field-item">
            <strong>카드 갯수</strong>
            <select title="카드 갯수" bind:value={$customCardAmount}>
              <option value="6">12개</option>
              <option value="9">18개</option>
              <option value="12">24개</option>
              <option value="15">30개</option>
              <option value="18">36개</option>
              <option value="21">42개</option>
            </select>
          </div>
          <div class="layer-field-item">
            <strong>보너스</strong>
            <select title="보너스" bind:value={$customBonusSec}>
              <option value="0">0초</option>
              <option value="1">1초</option>
              <option value="2">2초</option>
              <option value="3">3초</option>
              <option value="4">4초</option>
            </select>
          </div>
        </div>
        <div class="layer-field-controls">
          <Button buttonClassName={'btn-custom btn-start'} on:click={() => dispatch('customModeStart')}>
            START
          </Button>
          <Button buttonClassName={'btn-custom btn-cancel'} on:click={() => dispatch('eventCustomHide')}>
            CANCEL
          </Button>
        </div>
      </fieldset>
    </div>
  {:else}
    <div class="layer-body">
      <p class="layer-txt">
        - 같은 그림 카드 짝을 맞추는 게임입니다.<br><br>
        - 제한시간 내에 모든 카드의 짝을 맞추면 미션 성공입니다.<br><br>
        - 각 모드별로 제한시간은 EASY: 30초, NORMAL / HARD: 60초 입니다.<br><br>
        - 카드 짝을 맞출 때 마다 제한시간 보너스 2초를 획득합니다.<br><br>
        <strong>집중력을 발휘해서 모든 난이도를 성공 해보세요!</strong>
      </p>
    </div>
    <Button buttonClassName={'btn-info'} on:click={() => dispatch('eventGideHide')}>
      OK
    </Button>
  {/if}
  </div>
  {/if}
</div>

<style lang="scss" src="Layer.scss"></style>