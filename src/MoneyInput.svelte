<script>
  import { createEventDispatcher } from "svelte";
  export let currency = "EUR";
  export let value = "";
</script>

<style>
  input {
    padding-left: 1.5em;
    background-repeat: no-repeat;
    background-position: 0.25em center;
    background-size: 1em;
    background-image: url("/euro.svg");
  }
</style>

<input type="number" bind:value />