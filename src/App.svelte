<script>
  import Map from "./Components/Mapbox.svelte";
  import Legend from "./Components/Legend.svelte";
  import Button from "./Components/Button.svelte";
  import Slider from "@smui/slider";
  import Chart from "./Components/Barchart.svelte";
  import Modal from "svelte-simple-modal";
  import ModalAbout from "./Components/ModalAbout.svelte";
  import chroma from "chroma-js";
  import {
    base_colors,
    coordinates,
    year_min,
    year_max,
    bounds
  } from "./consts";

  let yeardiff = year_max - year_min;
  let palette = chroma
    .bezier(base_colors)
    .scale()
    .correctLightness()
    .colors(80);

  for (let i = 80; i < yeardiff; i++) {
    palette.push(palette[79]);
  }

  let map_palette = [];
  for (let i = 0; i < palette.length; i++) {
    map_palette.push(i);
    map_palette.push(palette[i]);
  }
  let map_palette_single = map_palette[11];

  let map_palette_sg = ["sg", "#E6007E"];

  let year = year_min;
  let single = false;
  let secondgrowth = false;

  function toggleSingle() {
    single = !single;
  }

  function toggleSecondgrowth() {
    console.log("hi")
    secondgrowth = !secondgrowth;
  }

  function addYear() {
    if (year != year_max) {
      year++;
    }
  }

  function minusYear() {
    if (year != year_min) {
      year--;
    }
  }
</script>

<style>
  a {
  color: #374151;
}
</style>

<header
  class="invisible absolute py-1 w-full bg-black text-gray-400 text-1.5 p-5
  bg-opacity-75 sm:visible" style="z-index: 2;">
  A History of Logging in Haida Gwaii 
  <Modal >
    <ModalAbout />
  </Modal>
  <div class = "absolute top-0 right-0 p-5 text-xs">
    <span class="text-gray-900 pr-1" >developed by </span>
    <a href="https://www.northbeachconsulting.ca" class="hover:no-underline hover:text-blue-600" target="_blank">North Beach Consulting</a>
  </div>
</header>

<div class="flex absolute bottom-0 w-full md:w-6/12 lg:w-6/12 xl:w-5/12" style="height: 40%;">
  <div class="w-full bg-black bg-opacity-75 p-2" style="z-index: 1;">
    <div class="absolute invisible sm:visible ">
      {#if !secondgrowth}
      <Button caption={'2nd growth'} on:second-growth={toggleSecondgrowth} />
    {:else}
      <Button caption={'hide 2nd growth'} on:second-growth={toggleSecondgrowth} />
    {/if}
      {#if !single}
        <Button caption={'by year'} on:single-year={toggleSingle} />
      {:else}
        <Button caption={'all years'} on:single-year={toggleSingle} />
      {/if}
    
    </div>
    <div class="text-center flex justify-center">
      <Button outline={false} caption={'-'} on:minus-year={minusYear} />
      <div class="inline-block">
        <p class="text-5xl text-gray-500">{year}</p>
      </div>
      <Button outline={false} caption={'+'} on:add-year={addYear} />
    </div>

    <div class="pl-5 w-10/12 -mt-2">
      <Slider class="mdc-slider" bind:value={year} min={1900} max={2017} step={1} discrete />
    </div>

    <div >
      <Chart {year} {single} {secondgrowth} {palette} {map_palette} {map_palette_single} {map_palette_sg}/>
      <!-- <D3Chart /> -->
    </div>
  </div>
</div>

<div class=" absolute right-0 sm:my-10  p-0 md:p-2 rounded-lg bg-black bg-opacity-75 text-gray-400" style="z-index: 1; ">
  <Legend {palette} {single} {secondgrowth} {map_palette_single} {map_palette_sg}/>
</div>


<Map {year} {single} {secondgrowth} {map_palette} {map_palette_single} {map_palette_sg} {bounds} />
