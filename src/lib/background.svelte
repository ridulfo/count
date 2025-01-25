<script lang="ts">
  import { blur } from "svelte/transition";
  export let onClick: () => void;
</script>

<div transition:blur on:click={onClick} class="background"></div>

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
</style>
