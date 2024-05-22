<template>
  <div>
    <label for="date-select">Seleziona una regione:</label>
    <select id="date-select" v-model="selectedDate" @change="updateChart">
      <option v-for="date in availableDates" :key="date" :value="date">{{ date }}</option>
    </select>
    <apexchart type="line" :options="chartOptions" :series="series" />
  </div>
</template>

<script>
import { read, utils } from 'xlsx';
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
            text: 'Temperature'
          }
        }
      },
      series: [],
      allData: [], // Dati completi dal file Excel
      availableDates: [], // Date disponibili
      selectedDate: null // Data selezionata dall'utente
    };
  },
  mounted() {
    this.loadExcelData();
  },
  methods: {
    async loadExcelData() {
      try {
        const response = await fetch('/dati2.xlsx');
        const data = await response.arrayBuffer();
        const workbook = read(data);
        const sheetName = workbook.SheetNames[0];
        const sheet = workbook.Sheets[sheetName];
        const json = utils.sheet_to_json(sheet, { header: 1 });

        this.processData(json);
      } catch (error) {
        console.error('Errore nel caricamento del file Excel:', error);
      }
    },
    processData(data) {
      // Salva tutti i dati
      this.allData = data;

      // Estrai le date disponibili (supponiamo che le date siano nella prima colonna)
      this.availableDates = [...new Set(data.slice(1).map(row => row[0]))];

      // Imposta la prima data come data selezionata di default
      this.selectedDate = this.availableDates[0];

      // Carica i dati per la data selezionata
      this.updateChart();
    },
    updateChart() {
  if (!this.selectedDate) return;

  // Filtra i dati in base alla data selezionata
  const filteredData = this.allData.filter(row => row[0] === this.selectedDate);

  if (filteredData.length > 0) {
    // Estrai le temperature
    const temperatures = filteredData[0].slice(1);

    // Estrai gli anni dalle date disponibili
    const years = this.availableDates.map(date => new Date(date).getFullYear());

    // Aggiorna il grafico con gli anni come categorie sull'asse x e i dati delle temperature
    this.chartOptions.xaxis.categories = years;
    this.series = [{
      name: 'Temperature',
      data: temperatures
    }];
  }
}

  }
};
</script>

<style>
/* Aggiungi eventuali stili qui */
</style>
