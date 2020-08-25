<template>
  <v-container>
    <v-card max-width="344" class="mx-auto pa-10">
      <v-text-field v-model="revenus" label="Revenus" suffix="€"></v-text-field>
      <v-text-field v-model="impot" label="Impôt" suffix="€"></v-text-field>
      <v-text-field
        v-model="apresImpot"
        label="Après impôts"
        suffix="€"
      ></v-text-field>
      <v-switch v-model="pacse" label="Pacsé ou marié"></v-switch>
      <v-text-field v-model="nbEnfant" label="Nombre d’enfant⋅s"></v-text-field>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: "HelloWorld",

  data() {
    return {
      revenus: 0,
      BAREME: {
        10065: 0,
        15595: 0.11,
        47710: 0.3,
        84437: 0.4,
      },
      pacse: false,
      nbEnfant: 0,
    };
  },

  computed: {
    impot() {
      let impot = 0;
      let revenus = parseFloat(this.revenus);
      revenus /= this.part;

      const tranches = Object.entries(this.BAREME);
      let i = 0;
      do {
        let tranche = revenus - tranches[i][0];
        console.log(revenus);
        if (tranche > 0) {
          impot += tranches[i][0] * tranches[i][1];
        } else {
          impot += revenus * tranches[i][1];
        }
        revenus -= tranches[i][0];
        i++;
      } while (revenus > 0 && i < 4);
      return Math.round(impot * this.part);
    },
    apresImpot() {
      return parseInt(this.revenus) - this.impot;
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
  },
};
</script>
