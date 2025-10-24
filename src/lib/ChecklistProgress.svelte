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

  // Track current checkbox states independently (current working version)
  let currentStates = items.map((i) => i.done);

  // Submitted/progress label state (updates on Submit)
  let submittedStates = [...currentStates];

  // Computed values
  $: completedCount = currentStates.filter(Boolean).length;
  $: percent = items.length === 0 ? 0 : (completedCount / items.length) * 100;

  // Milestone ticks at 25/50/75/100
  const milestonePercents = [25, 50, 75, 100];

  function handleItemChange(event, index) {
    currentStates[index] = event.detail.done;
    // reassign to trigger reactivity for arrays
    currentStates = [...currentStates];
  }

  function handleSubmit() {
    submittedStates = [...currentStates];
  }
</script>

<style>
  .stack {
    display: grid;
    gap: 0.75rem;
    align-items: start;
  }

  /* Progress bar */
  .progress {
    position: relative;
    width: 100%;
    height: 12px;
    border-radius: 999px;
    background: hsl(0 0% 92%);
    overflow: visible; /* allow tooltips to extend outside without clipping */
  }

  .progress__fill {
    position: absolute;
    inset: 0 auto 0 0;
    width: var(--pct, 0%);
    background: hsl(220 90% 56%);
    border-radius: 999px;
    transition: width 160ms ease-out;
    z-index: 1; /* sit under ticks */
  }

  .progress__ticks {
    position: absolute;
    inset: 0 0 0 0;
    pointer-events: none; /* container ignores events */
    z-index: 2; /* ensure ticks are above the fill */
  }

  .tick {
    position: absolute;
    top: 50%;
    width: 2px;
    height: 18px; /* a bit taller for visibility */
    background: hsl(0 0% 38%);
    transform: translateX(-50%) translateY(-50%);
    border-radius: 1px;
    pointer-events: auto; /* allow hover for tooltip */
    box-shadow: 0 0 0 1px rgba(255,255,255,0.6); /* subtle outline against blue fill */
  }

  .tick::after {
    /* Tooltip */
    content: attr(data-label);
    position: absolute;
    bottom: 100%;
    left: 50%;
    transform: translateX(-50%) translateY(-6px);
    white-space: nowrap;
    font-size: 12px;
    line-height: 1;
    background: hsl(0 0% 10%);
    color: white;
    padding: 6px 8px;
    border-radius: 6px;
    opacity: 0;
    visibility: hidden;
    transition: opacity 120ms ease-out, visibility 120ms ease-out, transform 120ms ease-out;
    max-width: 160px;
    text-align: center;
  }

  .tick:hover::after,
  .tick:focus-visible::after {
    opacity: 1;
    visibility: visible;
    transform: translateX(-50%) translateY(-10px);
  }

  /* Small-screen refinements to prevent tooltip crowding */
  @media (max-width: 420px) {
    .tick::after {
      font-size: 11px;
      padding: 4px 6px;
      max-width: 140px;
    }
  }

  ul {
    list-style: none;
    padding: 0;
    margin: 0.5rem 0 0 0;
    display: grid;
    gap: 0.5rem;
  }

  .counts {
    margin: 0.25rem 0 0.5rem 0;
    font-size: 0.95rem;
    color: hsl(0 0% 25%);
  }

  .versions {
    display: grid;
    gap: 0.25rem;
    font-size: 0.875rem;
    color: hsl(0 0% 34%);
  }

  button {
    appearance: none;
    border: 0;
    border-radius: 8px;
    padding: 0.6rem 0.9rem;
    background: hsl(220 90% 56%);
    color: white;
    font-weight: 600;
    cursor: pointer;
  }

  button:hover {
    filter: brightness(1.05);
  }
</style>

<div class="stack">
  <!-- Progress bar with fill and milestone ticks -->
  <div class="progress" style={`--pct: ${percent}%`}>
    <div class="progress__fill" role="progressbar"
      aria-valuemin="0"
      aria-valuemax="100"
      aria-valuenow={Math.round(percent)}
      aria-label="Checklist completion">
    </div>

    <div class="progress__ticks" aria-hidden="true">
      {#each milestonePercents as pct}
        <span
          class="tick"
          style={`left: ${pct}%`}
          data-label={`${pct}% milestone`}
          tabindex="-1"
          aria-hidden="true"
        ></span>
      {/each}
    </div>
  </div>

  <p class="counts">
    Completed: {completedCount}/{items.length} ({Math.round(percent)}%)
  </p>

  <!-- Example “submitted version” readout to show state difference if helpful -->
  <div class="versions" aria-live="polite">
    <div><strong>Current:</strong> {JSON.stringify(currentStates)}</div>
    <div><strong>Submitted:</strong> {JSON.stringify(submittedStates)}</div>
  </div>

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

  <button on:click={handleSubmit}>Submit version</button>
</div>
