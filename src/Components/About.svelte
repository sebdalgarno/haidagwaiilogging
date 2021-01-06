<script>
  export let data;
  export let tips;
</script>

<style>
  .title {
    @apply text-xl pt-4 pb-1;
  }

  .text {
    @apply text-sm text-gray-900;
  }

</style>

<div class="z-50">
  <p class="title">Tips for using the app</p>
  <p class="text">{tips}</p>
  <p class="title">Data</p>
  <p class="text">{data}</p>
  <p class="title">Attribution</p>
  <p class="text">App developed by Seb Dalgarno (<a href="https://www.northbeachconsulting.ca" target="_blank">North Beach Consulting</a>). It is a new implementation of John Broadhead's original animation and much of the original design is mimicked.</p>
</div>
