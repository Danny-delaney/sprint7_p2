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

  // Track current checkbox states independently
  let currentStates = items.map(i => i.done);

  // Progress label state (updates on Submit)
  let completedCount = 0;

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

<p>
  Completed: {completedCount}/{items.length} 
  ({Math.round((completedCount / items.length) * 100)}%)
</p>

<button on:click={handleSubmit}>Submit version</button>
