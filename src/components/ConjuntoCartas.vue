<template>
  <div>
    <div :class="'hand ' + claseTipoConjunto + ''">
      <CartaIndividual
        v-for="(carta, i) in conjuntoCartas"
        :key="i"
        @click.native="bajarCarta(i)"
        :numero="carta.numero"
        :palo="carta.palo"
        :comodin="carta.comodin"
        :color="carta.color"
        :codigo="carta.codigo"
        :posicion="carta.posicion"
        :visible="carta.visible"
      />
      <div v-if="tipo === 'seleccion'">
        <button @click="bajarJuego" v-if="valido">Bajar {{ tipoJuego }}</button>
        <button @click="bajarAlPozo" v-if="conjuntoCartas.length === 1">
          Al Pozo
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import CartaIndividual from "./CartaIndividual.vue";
export default {
  name: "ConjuntoCartas",
  components: { CartaIndividual },
  props: {
    conjuntoCartas: {
      type: Array,
      default: () => [],
    },
    seleccionadas: {
      type: Array,
      default: () => [],
    },
    tipo: {
      type: String,
      default: "mano",
    },
  },
  data() {
    return {
      valido: false,
      tipoJuego: null,
    };
  },
  computed: {
    cardsCodigos() {
      return this.conjuntoCartas.map((x) => x.codigo);
    },
    claseTipoConjunto() {
      return this.tipo === "mano" || this.tipo === "seleccion"
        ? "hhand-compact"
        : "hhand-mazo";
    },
  },
  watch: {
    conjuntoCartas: {
      handler(prev, after) {
        console.log(prev, after);
        if (prev.length !== after.length) {
          this.ordenar();
        }
        setTimeout(() => {
          if (this.tipo === "seleccion") this.validar();
        }, 100);
      },
    },
  },
  methods: {
    seleccionoCarta(carta) {
      let existe = this.seleccionadas.findIndex(
        (x) => x.codigo === carta.codigo
      );
      if (existe < 0) {
        let seleccionadasAux = this.seleccionadas;
        seleccionadasAux.splice(existe, 1);
        this.$emit("update:seleccionadas", [...seleccionadasAux]);
      } else {
        this.$emit("update:seleccionadas", [...this.seleccionadas, carta]);
      }
    },
    claseSeleccionada(carta) {
      return carta;
    },
    bajarJuego() {
      let juegoBajado = this.conjuntoCartas;
      this.$emit("juegoBajado", juegoBajado);
      this.$emit("update:conjuntoCartas", []);
    },
    bajarAlPozo() {
      this.$emit("cartaAlPozo", this.conjuntoCartas[0]);
      this.$emit("update:conjuntoCartas", []);
    },
    bajarCarta(i) {
      let cartaQuitada = this.quitarCarta(i);
      if (this.tipo === "seleccion") {
        this.$emit("cartaDevuelta", cartaQuitada);
      } else {
        this.$emit("cartaBajada", cartaQuitada);
      }
    },
    agregarCarta(carta) {
      this.$emit("update:conjuntoCartas", [...this.conjuntoCartas, carta]);
    },
    agregarCartas(cartas) {
      this.$emit("update:conjuntoCartas", [...this.conjuntoCartas, ...cartas]);
    },
    quitarCarta(posicion) {
      let cartasAux = this.conjuntoCartas;
      let cartaSaco = cartasAux.splice(posicion, 1)[0];
      this.$emit("update:conjuntoCartas", cartasAux);
      return cartaSaco;
    },
    quitarCartas(posiciones) {
      let cartasAux = this.conjuntoCartas;
      let cartasQuitadas = [];
      for (let i = 0; i < posiciones.length; i++) {
        cartasQuitadas.push(cartasAux.splice(posiciones[i], 1)[0]);
      }
      this.$emit("update:conjuntoCartas", cartasAux);
      return cartasQuitadas;
    },
    mezclar() {
      let cartasAux = this.conjuntoCartas;
      cartasAux = cartasAux.sort(() => 0.5 - Math.random());
      this.$emit("update:conjuntoCartas", cartasAux);
    },
    ordenar() {
      let cartasAux = this.conjuntoCartas;
      cartasAux = cartasAux.sort((a, b) => a.posicion - b.posicion);
      console.log("ordenadas", cartasAux);
      this.$emit("update:conjuntoCartas", cartasAux);
    },
    imprimir() {
      for (let i = 0; i < this.conjuntoCartas.length; i++) {
        console.log(
          this.conjuntoCartas[i].numero,
          this.conjuntoCartas[i].palo,
          this.conjuntoCartas[i].comodin,
          this.conjuntoCartas[i].posicion
        );
      }
    },
    validar() {
      this.valido = false;
      this.tipoJuego = null;
      console.log("asdas");
      if (this.conjuntoCartas.length < 3) {
        this.valido = false;
        return;
      }
      if (this.verificarPierna()) {
        this.valido = true;
        this.tipoJuego = "Pierna";
        console.log("JUJU");
        return;
      } else {
        console.log("NO ES PIERNA");
      }
      if (this.verificarEscalera()) {
        this.valido = true;
        this.tipoJuego = "Escalera";
        return;
      } else {
        console.log("NO ES ESCALERA");
      }
    },

    verificarPierna() {
      let nro = this.conjuntoCartas[0].numero;
      let palos = [];
      for (let i = 1; i < this.conjuntoCartas.length - 1; i++) {
        palos.push(this.conjuntoCartas[i].palo);
        if (this.conjuntoCartas[i].numero !== nro) {
          console.log(":::::::: ERROR::::::::: PALOsssss");
          return false;
        }
      }
      if (new Set(palos).size !== palos.length) {
        console.log(":::::::: ERROR::::::::: PALOaaaaaa");
        return false;
      }
      return true;
    },

    verificarEscalera() {
      let nros = [];
      let palo = this.conjuntoCartas[0].palo;
      for (let i = 0; i < this.conjuntoCartas.length; i++) {
        nros.push(this.conjuntoCartas[i].numero);
        if (palo !== this.conjuntoCartas[i].palo) {
          console.log(":::::::: ERROR::::::::: PALO");
          return false;
        }
        if (i == 0) {
          if (
            this.conjuntoCartas[i].posicion !==
            this.conjuntoCartas[i + 1].posicion - 1
          ) {
            console.log(":::::::: ERROR::::::::: 1");
            return false;
          }
        } else if (i > 0 && i < this.conjuntoCartas.length - 1) {
          if (
            this.conjuntoCartas[i].posicion !==
            this.conjuntoCartas[i + 1].posicion - 1
          ) {
            console.log(":::::::: ERROR::::::::: 2");
            return false;
          }
          if (
            this.conjuntoCartas[i].posicion !==
            this.conjuntoCartas[i - 1].posicion + 1
          ) {
            console.log(":::::::: ERROR::::::::: 3");
            return false;
          }
        } else if ((i === this, this.conjuntoCartas.length - 1)) {
          if (
            this.conjuntoCartas[i].posicion !==
            this.conjuntoCartas[i - 1].posicion + 1
          ) {
            console.log(":::::::: ERROR::::::::: 4");
            return false;
          }
        }
      }
      return true;
    },
  },
  mounted() {
    console.log(this.tipo);
  },
};
</script>

<style scoped>
.hand {
  padding: 5px 20px;
}
.hhand-mazo img.card {
  margin-left: -70.1px;
}
.hhand-compact img.card:hover {
  padding-top: 0;
  padding-bottom: 10px;
}
</style>
