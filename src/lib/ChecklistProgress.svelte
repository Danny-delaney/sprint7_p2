<script lang="ts">
  import ChecklistItem from '$lib/ChecklistItem.svelte';
  import { itemsStore, completedStore, percentStore, type Item } from '../stores';
  import { tweened } from 'svelte/motion';
  import { linear } from 'svelte/easing';

  $: items = $itemsStore as Item[]; 
  
  let completedCount = 0; 
  $: itemsTotal = items.length; 
  let percentComplete = 0;

  const animatedPercent = tweened(0, {
    duration: 1000,
    easing: linear
  });
  
  function handleSubmit()
  {
    completedCount = $completedStore;
    percentComplete = $percentStore;
    animatedPercent.set(percentComplete);
  }

  $: barWidth = `${$animatedPercent}%`; 
</script>

<h2>Checklist Progress</h2>

<ul>
  {#each items as item}
    <li>
      <ChecklistItem
        id={item.id}
        label={item.label}
        done={item.done} 
      />
    </li>
  {/each}
</ul>

<div class="progress-bar-container" data-testid="progress-bar-container">
  <div 
    class="progress-bar-snap"
    style="width: {percentComplete}%;"
    data-testid="progress-bar-snap"
  ></div>
  
  <div 
    class="progress-bar-animated"
    style="width: {barWidth};"
    data-testid="progress-bar-animated"
  ></div>
</div>

<p data-testid="progress-summary">
  Completed: {completedCount}/{itemsTotal} 
  ({percentComplete}%)
</p>

<button on:click={handleSubmit} data-testid="submit-button">Submit version</button>

<style>
  .progress-bar-container
  {
    height: 20px;
    background-color: #eee;
    margin-bottom: 20px;
    position: relative;
    border-radius: 4px;
    overflow: hidden;
  }

  .progress-bar-snap
  {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    background-color: #b3e5fc;
    transition: width 0s;
  }

  .progress-bar-animated
  {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    background-color: #03a9f4;
    transition: width 0s;
  }
</style>