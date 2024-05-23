<template>
  <div class="container">
    <label for="city-select" class="label">Seleziona una città:</label>
    <select id="city-select" v-model="selectedCity" @change="updateChart" class="select">
      <option value="">Seleziona una città</option>
      <option v-for="city in availableCities" :key="city" :value="city">{{ city }}</option>
    </select>
    <apexchart type="line" :options="chartOptions" :series="series" />
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
      chartOptions: {
        chart: {
          id: 'vuechart-example'
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
      series: [],
      allData: [], // Dati completi dal localStorage
      availableCities: [], // Città disponibili
      selectedCity: null // Città selezionata dall'utente
    };
  },
  mounted() {
    this.loadLocalStorageData();
  },
  methods: {
    loadLocalStorageData() {
      try {
        // Leggi i dati dal localStorage
        const localStorageData = localStorage.getItem('tableData2');

        if (localStorageData) {
          // Parsifica i dati JSON dal localStorage
          this.allData = JSON.parse(localStorageData);

          // Estrai i nomi delle città disponibili
          this.availableCities = this.allData.map(entry => entry.citta);

          // Carica i dati per la prima città disponibile
          this.selectedCity = this.availableCities[0];

          // Aggiorna il grafico per la città selezionata
          this.updateChart();
        } else {
          console.error('Dati non trovati nel localStorage.');
        }
      } catch (error) {
        console.error('Errore nel caricamento dei dati dal localStorage:', error);
      }
    },
    updateChart() {
      if (!this.selectedCity) return;

      // Filtra i dati in base alla città selezionata
      const selectedData = this.allData.find(entry => entry.citta === this.selectedCity);

      if (selectedData) {
        // Estrai le precipitazioni
        const precipitations = selectedData.dati.map(data => data.valore);

        // Estrai gli anni dalle date disponibili
        const years = selectedData.dati.map(data => data.data);

        // Aggiorna il grafico con gli anni come categorie sull'asse x e i dati delle precipitazioni
        this.chartOptions.xaxis.categories = years;
        this.series = [{
          name: 'Precipitazione',
          data: precipitations
        }];
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
</style>
