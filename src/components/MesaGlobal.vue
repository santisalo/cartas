<template>
  <div>
    <ConjuntoCartas
      v-for="(juego, j) in conjuntoJuegos"
      :conjuntoCartas="juego"
      :key="'j-' + j"
    />
  </div>
</template>

<script>
import ConjuntoCartas from "./ConjuntoCartas.vue";
export default {
  name: "MesaGlobal",
  components: { ConjuntoCartas },
  props: {
    conjuntoJuegos: {
      type: Array,
      default: () => [],
    },
  },
  mounted() {
    console.log(this.conjuntoJuegos);
  },
};
</script>

<style></style>
