<template>
  <ConjuntoCartas @click="sacarCarta" :conjuntoCartas="conjuntoCartas" />
</template>

<script>
import ConjuntoCartas from "./ConjuntoCartas.vue";
export default {
  name: "PozoCartas",
  props: {
    conjuntoCartas: {
      type: Array,
      default: () => [],
    },
  },
  computed: {
    cartasRenderizadas() {
      let renders = "";
      this.conjuntoCartas.forEach((el) => {
        renders += el.codigo + ",";
      });
      return renders;
    },
  },
  methods: {
    sacarCarta() {
      console.log("asdasdasdasdsasdsadas");
      let cartasAux = this.conjuntoCartas;
      let carta = cartasAux.splice(0, 1);
      this.$emit("update:conjuntoCartas", cartasAux);
      this.$emit("cartaSacada", carta);
    },
  },
  mounted() {
    console.log(this.conjuntoCartas);
  },
  components: { ConjuntoCartas },
};
</script>

<style></style>
