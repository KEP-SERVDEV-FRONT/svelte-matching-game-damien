<script>
import { customCardAmount, customSec, customBonusSec } from '@/store/stores';
import { onMount } from 'svelte';
import Progress from '@/components/Progress/Progress.svelte';
import Layer from '@/components/Layer/Layer.svelte';
import Card from '@/components/Card/Card.svelte';

enum LayerType {
  READY, 
  START, 
  GUIDE, 
  BLOCK, 
  CUSTOM
}

let 
easySize = 6,
hardSize = 15,
normalSize = 12,
bonusSeconds = 2,
progressPercent = 0,
prev = null,
cards: any[],
interval: any,
gameMode: string,
timeLimit: number,
correctCount: number,
maxCardAmount: number,
remainSeconds: number,
isDone: boolean,
isReady: boolean,
isStarted: boolean,
isVisible: boolean,
isComplete: boolean,
isCardDisabled: boolean,
isGuideVisible: boolean,
isTouchEnabled: boolean,
isCustomVisible: boolean,
isCustomMode: boolean;

$: isWarning = progressPercent > 50;
$: isDanger = remainSeconds < 10;
$: if (maxCardAmount === correctCount && maxCardAmount) gameComplete();
$: if (0 >= remainSeconds) gameOver();

const startTimeCheck = (seconds: number) => interval = setInterval(() => {
  remainSeconds -= .5;
  progressPercent = 0 > progressPercent ? 0 : 100 / seconds * (seconds - remainSeconds);
}, 500)

const createCards = (maxCount: number) => {
  const list = new Array(maxCount);
  const loopLimit = list.length;
  let i = 0;
  while (i < loopLimit) {
    list[i] = { id: i + 1, isOpened: false, isSame: false }
    i++;
  }

  cards = list
    .concat(JSON.parse(JSON.stringify(list)))
    .slice().sort(() => Math.random() > .5 ? 1 : -1);

  cards.forEach((item: any, index: number) => {
    setTimeout(() => {
      item.isOpened = true;
      cards = cards;
    }, index * 100);

    setTimeout(() => {
      item.isOpened = false;
      cards = cards;
    }, index * 100 + (100 * 7));
  });
}

const customStart = (customSec: number, customCardAmount: number, customBonusSec: number) => () => {
  bonusSeconds = +customBonusSec;
  gameStart(+customSec, +customCardAmount, bonusSeconds);
  isCustomMode = true;
}

const gameStart = (seconds: number, cardAmount: number, bonus: number) => {
  prev = null;
  isDone = false;
  isComplete = false;
  isCustomMode = false;
  isCustomVisible = false;
  isStarted = true;
  isTouchEnabled = true;
  correctCount = 0;
  progressPercent = 0;
  timeLimit = seconds;
  remainSeconds = seconds;
  bonusSeconds = bonus;
  maxCardAmount = cardAmount * 2;
  createCards(cardAmount);

  setTimeout(() => {
    startTimeCheck(seconds);
    isTouchEnabled = !isTouchEnabled;
    isVisible = false;
  }, cardAmount * 400);

  setTimeout(() => { 
    isVisible = !isVisible;
    isReady = false;
  }, (cardAmount * 400) - 2000);
}

const addBonusSeconds = (second: number): number => {
  return remainSeconds > timeLimit - second ? remainSeconds = timeLimit : remainSeconds += second;
}

const gameOver = () => {
  clearInterval(interval);
  isReady = true;
  isDone = true;
}

const gameComplete = () => {
  if (isCustomMode) {
    gameMode = 'CUSTOM';
  } else {
    gameMode = maxCardAmount / 2 >= hardSize ? 'HARD' : maxCardAmount / 2 >= normalSize ? 'NORMAL' : 'EASY';
  }
  isComplete = true;
  gameOver();
}

const openCard = (card: any) => {  
  if (card.isOpened) return;
  card.isOpened = !card.isOpened;
  cards = cards;
  if (prev === null) {
    prev = card;
    return
  }
  
  isCardDisabled = true;
  if (prev.id !== card.id) {
    setTimeout(() => {
      prev.isOpened = card.isOpened = false;
      isCardDisabled = false;
      prev = null;
      cards = cards
    }, 500);
  } else {
    setTimeout(() => {
      addBonusSeconds(bonusSeconds);
      correctCount += 2;
      prev.isOpened = card.isOpened = true;
      prev.isSame = card.isSame = true;
      isCardDisabled = false;
      prev = null;
      cards = cards;
    }, 500);
  }
}

onMount(() => {
  isReady = true;
})
</script>
<main class="main">
  <Progress 
    isDone={isDone}
    isDanger={isDanger}
    isStarted={isStarted}
    isWarning={isWarning}
    isComplete={isComplete}
    remainSeconds={remainSeconds}
    progressPercent={progressPercent}
  />

  <div class="wrap-card">
  {#if isStarted}
    {#each cards as card}
    <Card 
      id={card.id} 
      isSame={card.isSame}
      isOpened={card.isOpened} 
      on:click={() => openCard(card)}
    />
    {/each}
  {/if}
  </div>
</main>

{#if !isCustomVisible && (isReady || isDone)}
<Layer
  layerType={LayerType.READY}
  isDone={isDone}
  gameMode={gameMode}
  isStarted={isStarted}
  isComplete={isComplete}
  isCustomMode={isCustomMode}
  on:easyModeStart={() => gameStart(30, easySize, 2)}
  on:normalModeStart={() => gameStart(60, normalSize, 2)}
  on:hardModeStart={() => gameStart(60, hardSize, 2)}
  on:eventCustomVisible={() => isCustomVisible = true}
  on:eventGideVisible={() => isGuideVisible = true}
/>
{/if}

{#if isTouchEnabled}
<Layer
  layerType={LayerType.START}
  isVisible={isVisible}
/>
{/if}

{#if isGuideVisible}
<Layer
  layerType={LayerType.GUIDE}
  on:eventGideHide={() => isGuideVisible  = !isGuideVisible}
/>
{/if}

{#if isCardDisabled}
<Layer layerType={LayerType.BLOCK} />
{/if}

{#if isCustomVisible}
<Layer 
  layerType={LayerType.CUSTOM} 
  layerClassName={'layer-custom'}
  on:customModeStart={customStart($customSec, $customCardAmount, $customBonusSec)}
  on:eventCustomHide={() => isCustomVisible = false}
/>
{/if}

<style lang="scss" src="Main.scss"></style>