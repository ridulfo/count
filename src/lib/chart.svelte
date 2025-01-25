<script lang="ts">
  import Background from "./background.svelte";
  export let onClose: () => void;
  const title = window.location.hash.replace("#", "");
  type Count = {
    title: string;
    values: { datetime: string; increment: number }[];
  };
  const count: Count | null = JSON.parse(
    window.localStorage.getItem("count-" + title) || "[]",
  );
  const values = count?.values || [];

  // Calculate the increments per day starting from the first day
  // Fill in the missing days with 0 increments
  const incrementsPerDay = values.reduce((acc, { datetime, increment }) => {
    const day = new Date(datetime).setHours(0, 0, 0, 0);
    acc.set(day, (acc.get(day) || 0) + increment);
    return acc;
  }, new Map<number, number>());
  const firstDay = new Date([...incrementsPerDay.keys()].sort()[0]);
  const lastDay = new Date([...incrementsPerDay.keys()].sort().reverse()[0]);
  for (
    let day = new Date(firstDay);
    day <= lastDay;
    day.setDate(day.getDate() + 1)
  ) {
    if (!incrementsPerDay.has(day.setHours(0, 0, 0, 0))) {
      incrementsPerDay.set(day.valueOf(), 0);
    }
  }
</script>

<Background onClick={onClose} />

<div class="modal">
  <h2>Chart</h2>
  <div class="chart">
    {#each Array.from(incrementsPerDay.entries()).sort() as day}
      <div class="day">
        <span class="day-label">{new Date(day[0]).getDate()}</span>
        <div
          class="dot"
          style="height: 10px; width: 10px; 
             background-color: {day[1] > 0 ? 'black' : 'transparent'}; 
              border: 1px solid black;
             margin: 0 0 0.5rem 0;"
        ></div>
      </div>
    {/each}
  </div>
  <button on:click={onClose}>Close</button>
</div>

<style>
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
    max-width: 500px;
  }
  .chart {
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
    gap: 0.5rem;
  }
  .day {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background-color: black;
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
    width: 100%;
  }
</style>
