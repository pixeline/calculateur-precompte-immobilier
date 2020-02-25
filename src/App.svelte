<script>
  import { roundToTwo } from "./helpers.js";

  let rc_amount = 0;
  let tax_reductions = [
    {
      label: "habitation sociale appartenant aux CPAS et aux communes.",
      rate: 0.08
    },
    {
      label:
        "habitation dépendant de la Société Régionale bruxelloise du Logement",
      rate: 0.08
    },
    {
      label:
        "habitation dépendant du Fonds du Logement des Familles de la Région de Bruxelles-Capitale",
      rate: 0.08
    },
    {
      label:
        "habitation (partiellement) louée par une agences immobilière sociale (AIS) établie dans la Région de Bruxelles-Capitale",
      rate: 0
    }
  ];

  // Calculations
  $: tax_rate = 0.0125;
  $: precompte_amount_for_region =
    typeof rc_amount != "undefined" ? rc_amount * tax_rate : 0;
  $: precompte_amount_for_agglomeration = precompte_amount_for_region * 9.89;
  $: precompte_amount_for_commune = precompte_amount_for_region * 30;
  $: total_amount = roundToTwo(
    precompte_amount_for_region +
      precompte_amount_for_agglomeration +
      precompte_amount_for_commune
  );
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
    max-width: 996px;
  }
  .possible_reductions {
    text-align: left;
  }
  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  .final_result {
    font-size: 2rem;
    padding: 1rem 2rem;
  }
  .precompte_amount {
    border: 2px solid grey;
    padding-left: 1rem;
    padding-right: 1rem;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Calcule ton Précompte Immobilier</h1>

  <div>
    <label>
      Revenu cadastral indexé* du bien:
      <input type="number" name="rc_amount" bind:value={rc_amount} />
      EUR
    </label>
    <fieldset class="possible_reductions">
      <legend>Réductions possibles</legend>
      {#each tax_reductions as reduction}
        <label>
          <input name="tax_reductions" type="radio" value={reduction.rate} />
          {reduction.label}
        </label>
      {/each}
    </fieldset>
    <label>
      Commune du bien:
      <input type="text" />
    </label>
  </div>

  <div class="final_result">
    Précompte Immobilier:
    <span class="precompte_amount">{total_amount}</span>
    EUR
  </div>

</main>
