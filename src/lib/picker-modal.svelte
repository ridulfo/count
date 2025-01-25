<script lang="ts">
  let title = "";
  export let setTitle: (title: string) => void;
  export let availableTitles: string[] = [];
  export let onClose: () => void;
</script>
<div class="background"></div>

<div class="modal">
  <h2>Choose a title</h2>
  <select bind:value={title} on:change={(e) => setTitle(e.target.value)}>
    {#each availableTitles as title}
      <option value={title}>{title}</option>
    {/each}
  </select>
  <button on:click={() => setTitle(title)}>Use</button>
  <button on:click={onClose}>Close</button>
</div>

<style>
  .background {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 999;
  }
  .modal {
    font-family: sans-serif;
    position: fixed;
    background-color: var(--cornsilk);
    padding: 1rem;
    border-radius: 0.5rem;
    box-shadow: 10px 10px 0 black;
    z-index: 1000;
    display: flex;
    flex-direction: column;
    gap: 1rem;
    border: 2px solid black;
  }
  select {
    font-size: 1rem;
    padding: 0.5rem;
    border-radius: 0.25rem;
    width: 100%;
  }

  button {
    background-color: var(--cornsilk);
    color: black;
    box-shadow: 5px 5px 0 black;
    border: 2px solid black;
    padding: 10px 20px;
    border-radius: 5px;
    font-size: 1.5rem;
    cursor: pointer;
  }
</style>
