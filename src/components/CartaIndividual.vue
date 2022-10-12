<template>
  <img class="card" :src="'js/cards/' + renderizada + '.svg'" />
</template>

<script>
export default {
  name: "CartaIndividual",
  props: {
    numero: {
      type: String,
      default: null,
    },
    palo: {
      type: String,
      default: null,
    },
    comodin: {
      type: Boolean,
      default: null,
    },
    color: {
      type: String,
      default: null,
    },
    codigo: {
      type: String,
      default: null,
    },
    posicion: {
      type: Number,
      default: null,
    },
    visible: {
      type: Boolean,
      default: null,
    },
  },
  computed: {
    renderizada() {
      if (this.visible) {
        return this.codigo;
      } else {
        if (this.color === "Azul") {
          return "BLUE_BACK";
        } else {
          return "RED_BACK";
        }
      }
    },
  },
};
</script>
