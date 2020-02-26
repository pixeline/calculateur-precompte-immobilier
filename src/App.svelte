<script>
  import { roundToTwo } from "./helpers.js";

  let rc_amount = 0;
  let tax_reductions = [
    {
      label: "Aucune réduction",
      rate: 0.0125
    },
    {
      label: "habitation sociale appartenant aux CPAS et aux communes.",
      rate: 0.008
    },
    {
      label:
        "habitation dépendant de la Société Régionale bruxelloise du Logement",
      rate: 0.008
    },
    {
      label:
        "habitation dépendant du Fonds du Logement des Familles de la Région de Bruxelles-Capitale",
      rate: 0.008
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
    border: 1px dotted #555;
  }
  h1 {
    color: #0b3abf;
    text-transform: uppercase;
    font-size: 2rem;
    font-weight: 300;
  }
  h1 small {
    text-transform: none;
  }
  .rc_amount,
  .final_result {
    font-size: 2rem;
    padding: 1rem 2rem;
  }
  .rc_amount input {
    padding-left: 1rem;
    padding-right: 1rem;
  }
  .precompte_amount {
    border: 2px dotted grey;
    padding-left: 1rem;
    padding-right: 1rem;
  }

  footer {
    border-top: 1px dotted #333;
    padding: 2rem 0;
    font-size: 0.6rem;
  }
  .finger {
  }
  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>
    Calcule ton Précompte Immobilier
    <br />
    <small>en région de Bruxelles-Capitale</small>
  </h1>

  <div>
    <label class="rc_amount">
      Introduisez le revenu cadastral indexé du bien
      <span class="finger">☞</span>
      <input type="number" name="rc_amount" bind:value={rc_amount} />
      EUR
    </label>
    <fieldset class="possible_reductions">
      <legend>Réductions possibles</legend>
      {#each tax_reductions as reduction}
        <label>
          <input type="radio" bind:group={tax_rate} value={reduction.rate} />
          {reduction.label}
        </label>
      {/each}
    </fieldset>

  </div>

  <div class="final_result">
    Le précompte Immobilier à payer sera de
    <span class="precompte_amount">{total_amount}</span>
    EUR
  </div>
  <footer>
    made by
    <a href="https://pixeline.be" target="_blank">pixeline</a>
  </footer>
</main>
