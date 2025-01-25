<script lang="ts">
  import PickerModal from "./lib/picker-modal.svelte";
  let title = window.location.hash.replace("#", "");
  $: window.location.hash = title;
  // TODO: convert to a timestamp + increment for future-proofing
  $: count = getInitialCount(title);
  $: countShown = getCountTotal(title);
  let isChoosing = false;

  type Count = {
    title: string;
    values: { datetime: string; increment: number }[];
  };

  const getInitialCount = (title: string): Count => {
    const item = window.localStorage.getItem("count-" + title);
    if (!item) {
      return { title, values: [] };
    }
    return JSON.parse(item);
  };

  /** Get the total count
   * @param {string} _ Unused, but needed for reactivity #todo use effects
   */
  const getCountTotal = (_: string = ""): number => {
    return count.values.reduce((acc, { increment }) => acc + increment, 0);
  };

  const setCount = (title: string, count: Count) => {
    window.localStorage.setItem("count-" + title, JSON.stringify(count));
  };

  const increment = (increment: number) => (e: Event) => {
    count.values.push({ datetime: new Date().toISOString(), increment });
    setCount(title, count);
    countShown = getCountTotal();
    e.preventDefault();
    e.stopPropagation();
  };

  const allStorage = () => {
    const keys = [];
    for (let i = 0; i < window.localStorage.length; i++) {
      const key = window.localStorage.key(i);
      if (!key) continue;
      if (!key.startsWith("count-")) continue;
      keys.push(key.replace("count-", ""));
    }
    return keys.reverse();
  };
</script>

<main>
  <h2>Count</h2>
  <div class="input-wrapper">
    <input type="text" bind:value={title} placeholder="Enter title" />
    <button
      on:click={() => {
        isChoosing = true;
      }}>🔍</button
    >
  </div>
  {#if isChoosing}
    <PickerModal
      availableTitles={allStorage()}
      setTitle={(newTitle: string) => {
        isChoosing = false;
        title = newTitle;
      }}
      onClose={() => {
        isChoosing = false;
      }}
    />
  {/if}
  <span class="count">{countShown}</span>
  <button class="increment-button" on:click={increment(1)}>Increment</button>
  <button class="increment-button" on:click={increment(-1)}>Decrement</button>
</main>

<style>
  :root {
    --dark-moss-green: #606c38ff;
    --pakistan-green: #283618ff;
    --cornsilk: #dfcfb1ff /* #fefae0ff */;
    --earth-yellow: #dda15eff;
    --tigers-eye: #bc6c25ff;
  }
  :global(html),
  :global(body),
  :global(#app) {
    background-color: var(--dark-moss-green);
    height: 100%;
    width: 100%;
    font-family: "Georgia", serif;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
  }
  main {
    display: flex;
    flex-direction: column;
    gap: 20px;
    justify-content: center;
    align-items: center;
    background: var(--cornsilk);
    border-radius: 10px;
    margin: 20px;
    padding: 20px;
    border: 2px solid black;
    box-shadow: 10px 10px 0 rgba(0, 0, 0, 0.4);
    max-width: 500px;
    max-height: 500px;
  }

  .count {
    font-size: 4rem;
  }

  .increment-button {
    background-color: var(--cornsilk);
    color: black;
    box-shadow: 5px 5px 0 rgba(0, 0, 0, 0.5);
    border: 2px solid black;
    padding: 10px 20px;
    border-radius: 5px;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .input-wrapper {
    position: relative;
    width: 100%;
  }

  input {
    background-color: var(--cornsilk);
    color: black;
    border: 2px solid black;
    padding: 10px 40px 10px 10px; /* Add right padding for button space */
    border-radius: 5px;
    font-size: 1.5rem;
    cursor: pointer;
    width: 100%;
    text-align: center;
    box-sizing: border-box; /* Ensure padding doesn't affect width */
  }

  .input-wrapper button {
    position: absolute;
    top: 50%;
    right: 10px;
    transform: translateY(-50%);
    background: none;
    border: none;
    cursor: pointer;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .choosing-wrapper {
    display: flex;
    gap: 10px;
    width: 100%;
  }

  .choosing-wrapper button {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .choosing-wrapper select {
    flex-grow: 1;
    background-color: var(--cornsilk);
    color: black;
    border: 2px solid black;
    padding: 10px 40px 10px 10px; /* Add right padding for button space */
    border-radius: 5px;
    font-size: 1.5rem;
    cursor: pointer;
    width: 100%;
    text-align: center;
    box-sizing: border-box; /* Ensure padding doesn't affect width */
    width: auto;
  }
</style>
