<script lang="ts">
  import ListIcon from "./lib/list-icon.svelte";
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
  <h1 class="rubik-moonrocks-regular">Count</h1>
  <div class="input-wrapper">
    <input type="text" bind:value={title} placeholder="Enter title" />
    <button
      on:click={() => {
        isChoosing = true;
      }}><ListIcon /></button
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
  <div class="count-box">
    <button class="increment-button" on:click={increment(-1)}>-</button>
    <span class="count rubik-moonrocks-regular">{countShown}</span>
    <button class="increment-button" on:click={increment(1)}>+</button>
  </div>
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
    justify-content: space-evenly;
    align-items: center;
    background: var(--cornsilk);
    border-radius: 10px;
    margin: 20px;
    padding: 20px;
    border: 2px solid black;
    box-shadow: 10px 10px 0 black;
    max-width: 500px;
    max-height: 500px;
  }

  h1 {
    font-size: 3rem;
    margin: 0;
  }

  .count {
    font-size: 6rem;
  }

  .increment-button {
    align-items: center;
    background-color: var(--cornsilk);
    border-radius: 5px;
    border: 2px solid black;
    box-shadow: 5px 5px 0 black;
    color: black;
    cursor: pointer;
    font-family: roboto, sans-serif;
    font-size: 3rem;
    height: 75px;
    padding: 0;
    text-align: center;
    width: 75px;
  }

  .input-wrapper {
    position: relative;
    width: 100%;
  }

  input {
    font-family: sans-serif;
    background-color: var(--cornsilk);
    appearance: none;
    color: black;
    font-size: 2rem;
    cursor: text;
    width: 100%;
    text-align: center;
    box-sizing: border-box; /* Ensure padding doesn't affect width */
    border: none; /* Remove default border */
    border-bottom: 2px solid black; /* Add underline */
    padding: 0.5rem; /* Add some padding for a better look */
    outline: none; /* Remove outline on focus */
    transition: border-color 0.3s; /* Add smooth interaction effect */
  }

  input:focus {
    border-bottom: 2px solid var(--highlight-color, blue); /* Highlight color on focus */
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

  .count-box {
    display: flex;
    gap: 20px;
    align-items: center;
    justify-content: space-evenly;
    width: 100%;
  }

  .rubik-moonrocks-regular {
    font-family: "Rubik Moonrocks", serif;
    font-weight: 400;
    font-style: normal;
  }
</style>
