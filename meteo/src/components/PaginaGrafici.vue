<template>
  <div class="container">
    <!-- Contenitore principale -->
    <div class="charts-container">
      <!-- Primo Grafico -->
      <div class="chart-container">
        <label for="city-select1" class="label">Seleziona una città per le Temperature:</label>
        <select id="city-select1" v-model="selectedCity1" @change="updateChart1" class="select">
          <option value="">Seleziona una città</option>
          <option v-for="city in availableCities1" :key="city" :value="city">{{ city }}</option>
        </select>
        <apexchart type="line" :options="chartOptions1" :series="series1" class="large-chart"/>
      </div>

      <!-- Secondo Grafico -->
      <div class="chart-container">
        <label for="city-select2" class="label">Seleziona una città per le Precipitazioni:</label>
        <select id="city-select2" v-model="selectedCity2" @change="updateChart2" class="select">
          <option value="">Seleziona una città</option>
          <option v-for="city in availableCities2" :key="city" :value="city">{{ city }}</option>
        </select>
        <apexchart type="line" :options="chartOptions2" :series="series2" class="large-chart"/>
      </div>
    </div>
  </div>
</template>

<script>
import VueApexCharts from 'vue3-apexcharts';

export default {
  components: {
    apexchart: VueApexCharts,
  },
  data() {
    return {
      // Primo Grafico (Temperature)
      chartOptions1: {
        chart: {
          id: 'vuechart-temperature',
          width: '600px',  // Aumenta la larghezza del grafico
          height: '400px'  // Aumenta l'altezza del grafico
        },
        xaxis: {
          categories: []
        },
        title: {
          text: 'Temperature'
        },
        yaxis: {
          title: {
            text: 'Temperature'
          }
        }
      },
      series1: [],
      allData1: [], // Dati completi dal localStorage per le Temperature
      availableCities1: [], // Città disponibili per le Temperature
      selectedCity1: null, // Città selezionata dall'utente per le Temperature

      // Secondo Grafico (Precipitazioni)
      chartOptions2: {
        chart: {
          id: 'vuechart-precipitation',
          width: '600px',  // Aumenta la larghezza del grafico
          height: '400px'  // Aumenta l'altezza del grafico
        },
        xaxis: {
          categories: []
        },
        title: {
          text: 'Precipitazione'
        },
        yaxis: {
          title: {
            text: 'Precipitazione'
          }
        }
      },
      series2: [],
      allData2: [], // Dati completi dal localStorage per le Precipitazioni
      availableCities2: [], // Città disponibili per le Precipitazioni
      selectedCity2: null, // Città selezionata dall'utente per le Precipitazioni
    };
  },
  mounted() {
    this.loadLocalStorageData();
  },
  methods: {
    loadLocalStorageData() {
      // Leggi i dati dal localStorage per le Temperature
      const localStorageData1 = localStorage.getItem('tableData1');
      if (localStorageData1) {
        this.allData1 = JSON.parse(localStorageData1);
        this.availableCities1 = this.allData1.map(entry => entry.citta);
        this.selectedCity1 = this.availableCities1[0];
        this.updateChart1();
      } else {
        console.error('Dati non trovati nel localStorage per le Temperature.');
      }

      // Leggi i dati dal localStorage per le Precipitazioni
      const localStorageData2 = localStorage.getItem('tableData2');
      if (localStorageData2) {
        this.allData2 = JSON.parse(localStorageData2);
        this.availableCities2 = this.allData2.map(entry => entry.citta);
        this.selectedCity2 = this.availableCities2[0];
        this.updateChart2();
      } else {
        console.error('Dati non trovati nel localStorage per le Precipitazioni.');
      }
    },
    updateChart1() {
      if (!this.selectedCity1) return;
      const selectedData1 = this.allData1.find(entry => entry.citta === this.selectedCity1);
      if (selectedData1) {
        const temperatures = selectedData1.dati.map(data => data.valore).filter(temp => temp < 50);
        const years = selectedData1.dati.map(data => data.data);
        this.chartOptions1.xaxis.categories = years;
        this.series1 = [{ name: 'Temperature', data: temperatures }];
      }
    },
    updateChart2() {
      if (!this.selectedCity2) return;
      const selectedData2 = this.allData2.find(entry => entry.citta === this.selectedCity2);
      if (selectedData2) {
        const precipitations = selectedData2.dati.map(data => data.valore);
        const years = selectedData2.dati.map(data => data.data);
        this.chartOptions2.xaxis.categories = years;
        this.series2 = [{ name: 'Precipitazione', data: precipitations }];
      }
    }
  }
};
</script>

<style scoped>
.container {
  text-align: center;
}

.label {
  font-size: 20px;
}

.select {
  width: 300px;
  height: 40px;
  font-size: 16px;
  margin-top: 10px;
}

.large-chart {
  max-width: 800px;  /* Aumenta la larghezza massima */
  max-height: 600px; /* Aumenta l'altezza massima */
  margin: 0 auto;    /* Centra il grafico */
}

.charts-container {
  display: flex;
  justify-content: space-around;
}

.chart-container {
  flex: 1;
  margin: 20px;
  text-align: center;
}
</style>

