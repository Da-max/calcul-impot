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
    };
  },

  computed: {
    impot() {
      let impot = 0;
      let revenus = parseFloat(this.revenus);
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
      return Math.round(impot);
    },
    apresImpot() {
      return this.revenus - this.impot;
    },
  },
};
</script>
