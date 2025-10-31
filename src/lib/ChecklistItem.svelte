<script lang="ts">
  import { itemsStore, type Item } from '../stores';

  export let id: Item['id'];
  export let label: Item['label'];
  export let done: Item['done'] = false;

  function toggle() 
  {
    itemsStore.update(items => 
    {
      const item = items.find(i => i.id === id);
      if (item) 
      {
        item.done = !item.done;
      }
      return items; 
    });
  }
</script>

<label data-testid="checklist-item">
  <input type="checkbox" bind:checked={done} on:change={toggle} />
  {label}
</label>