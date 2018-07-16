<template>
  <div class="Bachillerato">
    <div class="Bachillerato-body" ref="bachillerato">
      <!-- Generacion de celdas header -->
      <div class="Bachillerato-header" :style="style">
        <div class="Bachillerato-item">
          <span>Letra</span>
        </div>

        <div
          class="Bachillerato-item"
          v-for="(category, i) in result"
          :key="`a-${i}`"
        >
          <span v-text="category.name"></span>
        </div>

        <div class="Bachillerato-item">
          <span>Total</span>
        </div>
      </div>

      <!-- Generacion de celdas normales -->
      <div class="Bachillerato-cells">
        <div
          class="Bachillerato-item"
          v-for="(n, i) in 20"
          :key="`b-${i}`"
        ></div>
      </div>
    </div>

    <!-- Controles -->
    <div class="Bachillerato-control">
      <h1>Generador de Bachilleratos</h1>

      <!-- Numero de categorias -->
      <div class="columns">
        <div class="column">
          <b-field label="Número de categorías">
            <b-input
              size="is-medium"
              type="number"
              v-model="categoriesNum"
              @input="calc()"
              min="4"
              :max="max"
            >
            </b-input>
          </b-field>
        </div>

        <!-- Nivel -->
        <div class="column">
          <b-field label="Nivel">
            <b-dropdown hoverable>
              <button class="button is-medium" slot="trigger">
                <span v-text="levelSelected.name"></span>
              </button>

              <b-dropdown-item
                v-for="(item, i) in levels"
                :key="`c-${i}`"
                @click="levelSelected = item; calc()"
                v-text="item.name"
              >
              </b-dropdown-item>
            </b-dropdown>
          </b-field>
        </div>
      </div>

      <!-- Acciones -->
      <div class="Bachillerato-actions">
        <button class="button is-primary is-rounded" @click="calc()">
          <b-icon icon="reload"></b-icon>
          <span>Aleatorio</span>
        </button>
        <button
          class="button is-danger is-outlined is-rounded"
          :class="{'is-loading': isPrinting}"
          :disabled="isPrinting"
          @click="print()"
        >
          <b-icon icon="cloud-print-outline"></b-icon>
          <span>Descargar</span>
        </button>

        <div class="Bachillerato-credits">
          <b-tooltip label="github.com/raulghm/bachillerato"
            type="is-dark"
            position="is-top">
            <a href="https://github.com/raulghm/bachillerato" target="_blank">
              <b-icon icon="github-circle"></b-icon>
            </a>
          </b-tooltip>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas'
import JsPDF from 'jspdf'
import data from '@/data'

export default {
  name: 'Bachillerato',

  data: () => ({
    categories: data, // datos categorias
    levels: [ // nivel de bachillerato
      { name: 'Todos', level: 10 },
      { name: 'Fácil', level: 0 },
      { name: 'Moderado', level: 1 },
      { name: 'Dificil', level: 2 }
    ],
    levelSelected: { name: 'Fácil', level: 0 }, // Nivel seleccionado
    categoriesNum: 5, // Numero de categoria seleccionada
    max: 7, // maximo de categorias
    maxRows: 10, // maximo de filas
    result: null, // resultados de categorias
    isPrinting: false // Estado imprimiendo
  }),

  mounted () {
    this.calc()
  },

  computed: {
    style () {
      return {
        'grid-template-columns': `
          minmax(40px, max-content)
          repeat(${this.categoriesNum}, auto)
          minmax(140px, max-content)
        `
      }
    }

    /**
     * Numero de celdas
     * Calcula las filas hacia abajo +
     * columna letra y total
     */
    // cells () {
    //   return (this.categoriesNum * this.maxRows) + (2 * this.maxRows)
    // }
  },

  methods: {
    /**
     * Calculo de categorias
     */
    calc () {
      // Todas las categorias
      if (this.levelSelected.level === 10) {
        this.result = this.categories
          .sort(() => 0.5 - Math.random())
          .slice(0, this.categoriesNum)
      } else {
        // Con categoria seleccionada
        this.result = this.categories
          .filter(cat => cat.level === this.levelSelected.level)
          .slice(0, this.categoriesNum)
          .sort(() => 0.5 - Math.random())
      }
    },

    /**
     * Se imprime!
     */
    async print () {
      this.isPrinting = true
      const canvas = await html2canvas(this.$refs.bachillerato)
      const data = canvas.toDataURL()

      const doc = new JsPDF(
        'l',
        'px',
        [canvas.width / 1.8,
          canvas.height / 1.8]
      )

      doc.addImage(data, 'PNG', 0, 0)
      doc.save('Bachillerato.pdf')
      this.isPrinting = false
    }
  }
}
</script>

<style>
.Bachillerato-body {
  height: 100vh;
  min-width: 1280px;
  min-height: 820px;
  overflow: hidden;
}

.Bachillerato h1 {
  font-family: 'Abril Fatface';
  font-weight: lighter;
  font-size: 2rem;
  letter-spacing: 2px;
  margin: 0 0 20px;
}

.Bachillerato-header {
  display: grid;
  grid-template-rows: fit-content(0);
  font-size: 14px;
}

.Bachillerato-header .Bachillerato-item {
  border-bottom: 3px solid #999;
  font-weight: bold;
  padding: 1.4em 2em;
}

.Bachillerato-header .Bachillerato-item:last-child {
  border-right: 0;
}

.Bachillerato-header .Bachillerato-item:after {
  content: '';
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  border-right: 1px solid #bbb;
  height: 200vh;
}

.Bachillerato-header .Bachillerato-item:last-child:after {
  border-right: 0;
}

.Bachillerato-item {
  padding: 2em;
  position: relative;
  text-align: center;
  border-bottom: 1px solid #999;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.Bachillerato-item span {
  max-width: 120px;
  display: inline-flex;
}

.Bachillerato-item:first-child span {
  min-width: auto;
}

/* Controles */

.Bachillerato-control {
  position: fixed;
  top: 50%;
  left: 50%;
  background-color: #886bd9;
  background: linear-gradient(135deg,#990fc3 0,#760fc3 16%,#652ec3 52%,#652ec3 52%, #990fc3 100%);
  transform: translateY(-50%) translateX(-50%);
  padding: 30px 40px;
  box-shadow: 0 0 9px 1px rgba(0,0,0,.4);
  border-radius: 10px;
  color: #fff;
}

.Bachillerato-control:after {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-image: url("https://d33wubrfki0l68.cloudfront.net/e18f62414900204e2edf2ba387c0ab220c0e7a52/bb198/static/img/bgr-page.svg");
  background-size: 300%;
  background-position: 50%;
  z-index: -1;
}

.Bachillerato-control .label {
  color: #fff;
}

.Bachillerato-control input {
  font-size: 2rem;
}

@media print {
  .Bachillerato-control {
    display: none;
  }
}

.Bachillerato .button {
  margin-right: 10px;
}

/* Actions */

.Bachillerato-actions {
  position: relative;
}

.Bachillerato-credits {
  position: absolute;
  right: 0;
  bottom: 0;
  font-size: 14px;
}

.Bachillerato-credits a {
  color: #fff;
}

.Bachillerato-credits .icon {
  vertical-align: top;
}
</style>
