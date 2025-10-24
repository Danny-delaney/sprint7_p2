<script>
  import { goto } from '$app/navigation';

  function handleClick() 
  {
    goto('/lab/checklist');
  }
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>

<button on:click={handleClick}>
  Go to Checklist Page
</button>
