<script>
  import { GetOffsetLeft, GetOffsetTop } from "../utils/Tools.svelte";
  import { onMount, onDestroy } from "svelte";

  export let items = [];
  export let value = undefined;
  export let numeric = true;
  export let Opener = undefined;
  export let Handler = undefined;
  export let Formatter = undefined;

  let show = false;
  let pos = [0.0, 0.0, 0.0];
  let opener = undefined;

  function OpenSelect(_e) {
    pos[0] = GetOffsetLeft(opener);
    pos[1] = GetOffsetTop(opener) + opener.offsetHeight;
    pos[2] = opener.offsetWidth;
    console.log(pos);
    show = !show;
    if (show && Opener) {
      Opener();
    }
    _e.preventDefault();
    _e.stopPropagation();
  }

  function CloseSelect(_e) {
    show = false;
  }

  onMount(()=>{
    document.addEventListener('click', CloseSelect);
  });

  onDestroy(()=>{
    document.removeEventListener('click', CloseSelect);
  });
</script>

<style>
  .opener,
  .select {
    font-size: 14px;
    font-family: mck-lato;
    line-height: 20px;
    text-align: center;
        box-shadow: 0px 1px 2px 0px #555;
  }
  .opener {
      background-color: #f0f0f0;
    border-radius: 2px;
    border: 1px solid #222;
    width: calc(100% - 10px);
    height: calc(100% - 10px);
    padding: 4px;
    cursor: pointer;
    overflow: hidden;
    display: grid;
    grid-template-columns: 1fr auto;
  }
  .opener:hover {
    background-color: #666;
    color: #f0f0f0;
  }
  .opener > .text {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .select {
    position: fixed;
    padding: 4px;
    display: grid;
    grid-auto-flow: row;
    background-color: #333;
    color: #f0f0f0;
    grid-row-gap: 4px;
    row-gap: 4px;
    z-index: 20;
  }
  .select > div:hover {
    background-color: #666;
    user-select: none;
    cursor: pointer;
  }

  .select > .active {
    background-color: #555;
  }
</style>

<div class="opener" bind:this={opener} on:click={OpenSelect} on:touchstart={OpenSelect}>
<span class="text">
  {#if value === undefined || value === ''}
    <i>Select</i>
  {:else if numeric}
    {#if Formatter}{Formatter(items[value])}{:else}{items[value]}{/if}
  {:else if Formatter}{Formatter(value)}{:else}{value}{/if}
  </span>
  <span class="{show ? 'mck-arrow_drop_up': 'mck-arrow_drop_down'}"/>
</div>
{#if show}
  <div class="select" style="left: {pos[0]}px; top: {pos[1]}px; min-width: {pos[2]}px;">
    {#each items as item, i}
      <div
        class="{value === i ? 'active' : ''}"
        on:click={() => {
          if (numeric) {
            Handler(i);
          } else {
            Handler(item);
          }
          show = false;
        }}
        on:touchstart={() => {
          if (numeric) {
            Handler(i);
          } else {
            Handler(item);
          }
          show = false;
        }}>
        {item}
      </div>
    {/each}
  </div>
{/if}
