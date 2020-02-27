<script>
  import { roundToTwo, stripHtml, nl2br } from "./helpers.js";
  import MoneyInput from "./MoneyInput.svelte";
  let rc_amount = 0;
  let tax_rates = [
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

  let other_reductions = [
    { label: "aucune", description: "", percent: 0 },
    {
      label: "Maison Modeste 1",
      description:
        "L’habitation doit:\n\t- Etre le seul bien immobilier dans la Région de Bruxelles-Capitale sur lequel le redevable conserve un droit réel\n\t- Etre le domicile du redevable\n\t- Avoir un revenu cadastral non-indexé dont le montant ne dépasse pas 745.\n",
      percent: 25
    },
    {
      label: "Maison Modeste 2",
      description:
        "- Il s’agit d’un bien neuf\n- Vous n’avez pas bénéficiez d’une prime à la construction ou à l’achat.\n\nLa réduction de 50% n’est valable que pour une durée de 5 ans maximum.",
      percent: 50
    },
    {
      label: "Personne/enfant handicapé(e) ou invalide de guerre",
      description:
        "Le chef de ménage ou un membre du ménage domicilié dans le bien est reconnu comme handicapé ou grand invalide de guerre. Attention, les deux ne sont pas cumulables.",
      percent: 20
    },
    {
      label: "Enfants ouvrant le droit aux allocations familiales",
      description:
        "Le ménage est composé d’au moins 2 enfants domiciliés dans le bien et ouvrant le droit aux allocations familiales. Un partage proportionnel de la réduction est cependant possible en cas de garde partagée.",
      percent: 10
    },
    {
      label: "Façade classée",
      description: "Façade classée ou sur la liste de sauvegarde (25%)",
      percent: 25
    },
    {
      label: "Jardin classé",
      description:
        "Intérieur ou jardin classé ou sur la liste de sauvegarde (50%)",
      percent: 50
    },
    {
      label: "Bien classé totalement",
      description: "Classé totalement ou sur la liste de sauvegarde (100%)",
      percent: 100
    }
  ];

  // Calculations
  $: tax_rate = 0.0125;
  $: precompte_amount_for_region =
    typeof rc_amount != "undefined" ? rc_amount * tax_rate : 0;
  $: precompte_amount_for_agglomeration = precompte_amount_for_region * 9.89;
  $: precompte_amount_for_commune = precompte_amount_for_region * 30;
  // Other reductions
  $: other_reduction = 0;

  //
  let total_amount = 0;
  let final_amount = 0;
  $: {
    total_amount =
      precompte_amount_for_region +
      precompte_amount_for_agglomeration +
      precompte_amount_for_commune;
    final_amount =
      other_reduction > 0
        ? roundToTwo(total_amount - total_amount * (other_reduction / 100))
        : roundToTwo(total_amount);
  }
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
    max-width: 996px;
    font-family: -apple-system, system-ui, "Segoe UI", Roboto, Oxygen, Ubuntu,
      Cantarell, "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
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

  .precompte_amount {
    border: 2px dotted grey;
    padding-left: 1rem;
    padding-right: 1rem;
  }
  .row {
    display: flex;
  }

  .column {
    flex: 50%;
  }
  footer {
    border-top: 1px dotted #333;
    padding: 2rem 0;
    font-size: 0.6rem;
  }
  .info {
    border-radius: 50%;
    padding: 2px 7px;
    background: grey;
    color: white;
    font-size: 0.8rem;
  }
  label {
    line-height: 100%;
    margin-bottom: 0.6rem;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>
    Calculez votre Précompte Immobilier
    <br />
    <small>en région de Bruxelles-Capitale</small>
  </h1>

  <div>
    <label class="rc_amount">
      Introduisez le revenu cadastral indexé du bien
      <span class="finger">☞</span>
      <MoneyInput bind:value={rc_amount} currency="EUR" />
    </label>
    <div class="row">
      <fieldset class="possible_reductions column">
        <legend>Réductions possibles</legend>
        {#each tax_rates as { label, rate }, i}
          <label>
            <input type="radio" bind:group={tax_rate} value={rate} />
            {label}
          </label>
        {/each}
      </fieldset>
      <fieldset class="possible_reductions column">
        <legend>Réductions complémentaires</legend>
        {#each other_reductions as { label, description, percent }, i}
          <label>
            <input type="radio" bind:group={other_reduction} value={percent} />
            {label}
            <small>( {percent} %)</small>
            {#if description.length > 0}
              <span class="info" title={description}>?</span>
            {/if}
          </label>
        {/each}
      </fieldset>
    </div>

  </div>

  <div class="final_result">
    Le précompte Immobilier à payer sera de
    <span class="precompte_amount">{final_amount}</span>
    EUR
  </div>
  <footer>
    source:
    <a
      href="https://fiscalite.brussels/fr/le-precompte-immobilier"
      target="_blank">
      fiscalite.brussels
    </a>
    made by
    <a href="https://pixeline.be" target="_blank">pixeline</a>
  </footer>
</main>
