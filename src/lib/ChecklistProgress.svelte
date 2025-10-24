<script>
  import ChecklistItem from '$lib/ChecklistItem.svelte';

  // Example checklist items
  let items = [
    { id: 1, label: 'Item 1', done: false },
    { id: 2, label: 'Item 2', done: false },
    { id: 3, label: 'Item 3', done: false },
    { id: 4, label: 'Item 4', done: false },
    { id: 5, label: 'Item 5', done: false },
  ];

  let currentStates = items.map(i => i.done);
  let completedCount = 0;
  let target = 80;
  $: currentPercentage = Math.round((completedCount / items.length) * 100);
  $: goalReached = currentPercentage >= target;

  // Called whenever a checkbox changes
  function handleItemChange(event, index) {
    currentStates[index] = event.detail.done;
  }

  // Called on Submit to calculate completion
  function handleSubmit() {
    completedCount = currentStates.filter(done => done).length;
  }
</script>

<h2>Checklist Progress</h2>

<ul>
  {#each items as item, index}
    <li>
      <ChecklistItem
        id={item.id}
        label={item.label}
        done={currentStates[index]}
        on:change={(e) => handleItemChange(e, index)}
      />
    </li>
  {/each}
</ul>

<!-- Goal Line Indicator -->
<div class="goal-indicator">
  <div class="goal-label">
    Goal: <input type="number" bind:value={target} min="0" max="100" />%
  </div>
  
  <div class="progress-container">
    <div class="progress-bar" style="width: {currentPercentage}%"></div>
    <div 
      class="goal-marker" 
      class:reached={goalReached}
      style="left: calc({target}% - 2px)"
      title="Target: {target}%"
    ></div>
  </div>
  
  <div class="progress-text">
    {#if goalReached}
      <span class="success">✓ Goal reached!</span>
    {:else}
      <span>{target - currentPercentage}% to goal</span>
    {/if}
  </div>
</div>

<button on:click={handleSubmit}>Submit version</button>

<style>
  ul {
    list-style: none;
    padding: 0;
  }

  li {
    margin: 0.5rem 0;
  }

  button {
    margin-top: 1rem;
    padding: 0.5rem 1rem;
    cursor: pointer;
  }

  .goal-indicator {
    margin: 1.5rem 0;
    padding: 1rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    background: #f9f9f9;
  }

  .goal-label {
    margin-bottom: 0.5rem;
    font-weight: 600;
  }

  .goal-label input {
    width: 50px;
    padding: 2px 4px;
    margin: 0 4px;
  }

  .progress-container {
    position: relative;
    height: 30px;
    background: #e0e0e0;
    border-radius: 4px;
    overflow: visible;
  }

  .progress-bar {
    height: 100%;
    background: #4CAF50;
    border-radius: 4px;
    transition: width 0.3s ease;
  }

  .goal-marker {
    position: absolute;
    top: -2px;
    width: 4px;
    height: 34px;
    background: #ff9800;
    border-radius: 2px;
    transition: background-color 0.3s ease;
    cursor: pointer;
  }

  .goal-marker.reached {
    background: #4CAF50;
  }

  .progress-text {
    margin-top: 0.5rem;
    font-size: 0.9rem;
  }

  .success {
    color: #4CAF50;
    font-weight: 600;
  }
</style>