<template>
  <ConjuntoCartas
    class="cCartas"
    tipo="mazo"
    :conjuntoCartas="conjuntoCartas"
    @click.native="sacarCarta"
  />
</template>

<script>
import ConjuntoCartas from "./ConjuntoCartas.vue";
export default {
  name: "MazoCartas",
  components: { ConjuntoCartas },
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
        renders += el.color === "Azul" ? "BLUE_BACK," : "RED_BACK,";
      });
      return renders;
    },
  },
  mounted() {
    console.log(this.conjuntoCartas);
  },
  methods: {
    generarMazo() {
      let mazo = [];
      let numeros = [
        "A",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "J",
        "Q",
        "K",
      ];
      let colores = ["Azul", "Rojo"];
      let palos = ["C", "D", "H", "S"];
      for (let colorI = 0; colorI < colores.length; colorI++) {
        for (let numeroI = 0; numeroI < numeros.length; numeroI++) {
          for (let paloI = 0; paloI < palos.length; paloI++) {
            numeros[numeroI], palos[paloI], false, colores[colorI], numeroI;
            mazo.push({
              numero: numeros[numeroI],
              palo: palos[paloI],
              comodin: false,
              color: colores[colorI],
              codigo: numeros[numeroI] + palos[paloI],
              posicion: numeroI,
              visible: false,
            });
          }
        }
      }
      console.log("MAZO OK", mazo);
      this.$emit("update:conjuntoCartas", [...mazo]);
    },
    mezclar() {
      return new Promise((resolve) => {
        let cartasAux = this.conjuntoCartas;
        cartasAux = cartasAux.sort(() => 0.5 - Math.random());
        console.log("mezcladas", cartasAux);
        this.$emit("update:conjuntoCartas", cartasAux);
        resolve();
      });
    },
    repartir(cantidadJugadores, cantidadCartas) {
      let cartasRepartidas = [];
      let cartasAux = this.conjuntoCartas;
      for (let i = 0; i < cantidadJugadores; i++) {
        cartasRepartidas.push([]);
        for (let j = 0; j < cantidadCartas; j++) {
          let carta = cartasAux.splice(0, 1)[0];
          carta.visible = true;
          cartasRepartidas[i].push(carta);
        }
      }
      this.$emit("update:conjuntoCartas", cartasAux);
      this.$emit("repartirCartas", cartasRepartidas);
    },
    sacarCarta() {
      console.log("asdasdasdasdsasdsadas");
      let cartasAux = this.conjuntoCartas;
      let carta = cartasAux.splice(0, 1)[0];
      carta.visible = true;
      this.$emit("update:conjuntoCartas", cartasAux);
      this.$emit("cartaSacada", carta);
    },
  },
};
</script>

<style scoped>
.cCartas {
  display: flex;
  justify-content: end;
}
</style>
