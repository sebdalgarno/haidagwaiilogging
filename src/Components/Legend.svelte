<script>
  export let palette;
  export let single;
  export let secondgrowth;
  export let map_palette_single;
  export let map_palette_sg;

  let legend_palette = [
    { value: "0", color: palette[0] },
    { value: "20", color: palette[20] },
    { value: "40", color: palette[40] },
    { value: "60", color: palette[60] },
    { value: ">80", color: palette[80] }

    // { value: 116, color: palette[116] }
  ];

  let legend_single = [
    { value: "Harvested", color: map_palette_single }
    // { value: 116, color: palette[116] }
  ];

  let legend_sg = [
    {value: "2nd Growth", color: map_palette_sg[1]}
  ];
</script>

<style>
  .legend {
    @apply text-gray-600 p-2 z-50 text-xs;
  }

  .legendspan {
    @apply inline-block h-2 mr-1 w-3 text-xs;
  }
</style>

{#if single}
  <div class="legend">
    {#each legend_single as { value, color }}
      <div>
        <span class="legendspan" style="background-color: {color}" />
        {value}
      </div>
    {/each}
  </div>
{:else}
  <div class="legend">
    <div class="text-xs">
      Years Since
      <br />
      Harvested
    </div>
    {#each legend_palette as { value, color }}
      <div>
        <span class="legendspan" style="background-color: {color}" />
        {value}
      </div>
    {/each}
  </div>
{/if}
{#if secondgrowth}
  <div class="legend -my-3">
    {#each legend_sg as { value, color }}
      <div>
        <span class="legendspan" style="background-color: {color}" />
        {value}
      </div>
    {/each}
  </div>
  {/if}
