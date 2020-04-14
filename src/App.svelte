<script>
  import Connect from "./Connect.svelte";
  import AddData from "./AddData.svelte";

  let isGood = false;
  let item = Connect;
</script>

{#if !isGood}
  <Connect bind:isGood />
{:else}
  <AddData />
{/if}
