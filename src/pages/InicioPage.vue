<template>
  <div class="layout" :key="keyGral">
    <div class="mesa">
      <MesaGlobal :conjuntoJuegos.sync="juegos" />
    </div>
    <div class="mesa-aux">
      <ConjuntoCartas
        :conjuntoCartas.sync="seleccionadasAux"
        @cartaDevuelta="cartaDevuelta"
        tipo="seleccion"
        :valido="seleccionValido"
        :tipoJuego="seleccionTipoJuego"
        @juegoBajado="juegoBajado"
        @cartaAlPozo="cartaAlPozo"
        ref="seleccionAux"
      />
    </div>
    <div class="mesa-mazo">
      <PozoCartas :conjuntoCartas.sync="pozo" />
      <MazoCartas
        class="mazo"
        :conjuntoCartas.sync="mazo"
        ref="ccMazo"
        @repartirCartas="repartirCartas"
        @cartaSacada="cartaSacada"
      />
    </div>
    <div class="j1 contenedor-mano">
      <ConjuntoCartas
        @cartaBajada="cartaBajada"
        :conjuntoCartas.sync="jugadores[0].mano"
        :seleccionadas.sync="jugadores[0].seleccionadas"
        ref="ccJ1"
        :key="kccJ1"
      />
    </div>
    <div class="j2 contenedor-mano">
      <ConjuntoCartas
        @cartaBajada="cartaBajada"
        :conjuntoCartas.sync="jugadores[1].mano"
        :seleccionadas.sync="jugadores[1].seleccionadas"
        ref="ccJ2"
        :key="kccJ2"
      />
    </div>
    <div class="j3 contenedor-mano">
      <ConjuntoCartas
        @cartaBajada="cartaBajada"
        :conjuntoCartas.sync="jugadores[2].mano"
        :seleccionadas.sync="jugadores[2].seleccionadas"
        ref="ccJ3"
        :key="kccJ3"
      />
    </div>
    <div class="j4 contenedor-mano">
      <ConjuntoCartas
        @cartaBajada="cartaBajada"
        :conjuntoCartas.sync="jugadores[3].mano"
        :seleccionadas.sync="jugadores[3].seleccionadas"
        ref="ccJ4"
        :key="kccJ4"
      />
    </div>
  </div>
</template>

<script>
import "cardsJS/dist/cards.min.css";
import "cardsJS/dist/cards";
import ConjuntoCartas from "@/components/ConjuntoCartas.vue";
import MesaGlobal from "@/components/MesaGlobal.vue";
import PozoCartas from "@/components/PozoCartas.vue";
import MazoCartas from "@/components/MazoCartas.vue";
export default {
  components: { ConjuntoCartas, MesaGlobal, PozoCartas, MazoCartas },

  data() {
    return {
      juegos: [],
      pozo: [],
      mazo: [],
      jugadores: [
        { mano: [], seleccionadas: [] },
        { mano: [], seleccionadas: [] },
        { mano: [], seleccionadas: [] },
        { mano: [], seleccionadas: [] },
      ],
      seleccionadasAux: [],
      seleccionValido: false,
      seleccionTipoJuego: null,
      keyGral: 1,
      kccJ1: 1,
      kccJ2: 1,
      kccJ3: 1,
      kccJ4: 1,
      turnoActual: 0,
    };
  },
  async mounted() {
    await this.$refs.ccMazo.generarMazo();
    await this.$refs.ccMazo.mezclar();
    this.$refs.ccMazo.repartir(4, 9);
    this.keyGral++;
    console.log(this.jugadores);
    console.log(this.mazo);
  },
  methods: {
    repartirCartas(conjuntoCartas) {
      conjuntoCartas.forEach((cartas, i) => {
        this.jugadores[i].mano = cartas;
        setTimeout(() => {
          this.$refs["ccJ" + (i + 1)].ordenar();
        }, 100);
      });
      this.keyGral++;
      console.log(conjuntoCartas);
    },
    cartaSacada(carta) {
      this.jugadores[this.turnoActual].mano.push(carta);
      setTimeout(() => {
        this.$refs["ccJ" + (this.turnoActual + 1)].ordenar();
      }, 100);
      this["kccJ" + (this.turnoActual + 1)]++;
    },
    cartaBajada(carta) {
      this.seleccionadasAux.push(carta);
    },
    cartaDevuelta(carta) {
      this.jugadores[this.turnoActual].mano.push(carta);
      this["kccJ" + (this.turnoActual + 1)]++;
    },
    juegoBajado(cartas) {
      this.juegos.push(cartas);
    },
    cartaAlPozo(carta) {
      this.pozo.push(carta);
    },
  },
};
</script>

<style scoped>
.contenedor-mano {
  display: flex;
  justify-content: center;
  align-items: center;
}

.hand {
  padding: 5px 20px;
}
.pozo {
}

.layout {
  height: 100%;
  display: grid;
  grid-template-columns: 1.25fr repeat(2, 2fr) 1.25fr;
  grid-template-rows: 1fr repeat(2, 2fr) repeat(2, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}

.mesa {
  position: relative;
  padding: 25px;
  background-color: green;
  grid-area: 2 / 2 / 4 / 4;
}
.j1 {
  grid-area: 5 / 2 / 6 / 4;
}
.j2 {
  grid-area: 2 / 4 / 5 / 5;
}
.j3 {
  grid-area: 1 / 2 / 2 / 4;
}
.j4 {
  grid-area: 2 / 1 / 5 / 2;
}
.mesa-aux {
  grid-area: 4 / 2 / 5 / 3;
  background-color: rgb(2, 95, 2);
}
.mesa-mazo {
  display: flex;
  grid-area: 4 / 3 / 5 / 4;
  background-color: rgb(28, 141, 28);
}
.mazo {
  margin-left: 75px;
}
</style>
