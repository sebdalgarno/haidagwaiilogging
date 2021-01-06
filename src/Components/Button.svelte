<script>
  import { createEventDispatcher } from "svelte";

  export let caption;
  export let toggle = false;
  export let outline = true;

  const dispatch = createEventDispatcher();

  function singleYear() {
    dispatch("single-year");
  }

  function secondGrowth() {
    dispatch("second-growth");
  }

  function addYear() {
    dispatch("add-year");
  }

  function minusYear() {
    dispatch("minus-year");
  }

  function openModal() {
    dispatch("open-modal");
  }
</script>

<style>
  button:focus {
    outline: none;
  }
</style>

{#if outline}
  <button
    class="border-gray-700 p-2 text-gray-600 rounded-lg text-xs leading-none hover:text-blue-600 my-1"
    on:click={singleYear}
    on:click={secondGrowth}>
    {caption}
  </button>
{:else}
  <button
    class="p-3 border-0 text-gray-600 text-xs hover:text-blue-600"
    on:click={addYear}
    on:click={minusYear}
    on:click={openModal}>
    {caption}
  </button>
{/if}
