<template>
  <v-card max-width="544" class="mx-auto pa-10">
    <v-card-title>Calcul du revenus net à partir du revenus brut</v-card-title>
    <v-text-field
      v-model="revenus"
      label="Revenus"
      suffix="€"
      :rules="numberRules"
    ></v-text-field>
    <v-text-field
      v-model="impot"
      label="Impôt"
      suffix="€"
      :disabled="true"
    ></v-text-field>
    <v-text-field
      v-model="apresImpot"
      label="Après impôts"
      suffix="€"
      :disabled="true"
    ></v-text-field>
    <v-switch v-model="pacse" label="Pacsé ou marié"></v-switch>
    <v-text-field v-model="nbEnfant" label="Nombre d’enfant⋅s"></v-text-field>
    <v-list>
      <v-list-item
        >Revenus imposable : {{ revenus }} ÷ {{ part }} =
        {{ Math.round(revenus / part) }}</v-list-item
      >
      <v-list-item v-for="detail in details" :key="detail.coef">
        {{ detail.quantite }} x {{ detail.coef }} = {{ detail.payer }} €
      </v-list-item>
      <v-list-item
        >Soit : {{ impotSansPart }} x {{ part }} = {{ impot }} €</v-list-item
      >
    </v-list>
  </v-card>
</template>

<script>
export default {
  name: "revenusBrut",

  data() {
    return {
      revenus: 0,
      tranches: [
        { max: 10065, coef: 0 },
        { max: 15595, coef: 0.11 },
        { max: 47710, coef: 0.3 },
        { max: 84437, coef: 0.41 },
        { max: Infinity, coef: 0.45 },
      ],
      pacse: false,
      nbEnfant: 0,
      numberRules: [
        (v) => Number(v) == v || "Ce champ doit contenir un nombre.",
      ],
      details: [],
    };
  },

  computed: {
    impot() {
      this.details = [];
      let impot = 0;
      let revenus = parseFloat(this.revenus);
      revenus /= this.part;

      this.tranches.forEach((tranche) => {
        if (revenus > tranche.max) {
          const payer = tranche.max * tranche.coef;
          impot += payer;
          revenus -= tranche.max;
          this.details.push({
            quantite: tranche.max,
            coef: tranche.coef,
            payer: Math.round(payer),
          });
        } else {
          const payer = revenus * tranche.coef;
          impot += payer;
          this.details.push({
            quantite: revenus,
            coef: tranche.coef,
            payer: Math.round(payer),
          });
          revenus = 0;
        }
      });
      return Math.round(impot * this.part) || 0;
    },
    apresImpot() {
      return parseInt(this.revenus) - this.impot || 0;
    },
    part() {
      let part = 1;
      if (this.pacse) {
        part++;
      }
      if (parseInt(this.nbEnfant) > 0) {
        part += this.nbEnfant / 2;
      }
      return part;
    },
    impotSansPart() {
      let total = 0;
      this.details.forEach((detail) => {
        total += detail.payer;
      });
      return total;
    },
  },
};
</script>
