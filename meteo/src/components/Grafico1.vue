<template>
    <div>
      <select v-model="selectedYear" @change="updateChart">
        <option v-for="year in availableYears" :key="year">{{ year }}</option>
      </select>
      <apexchart v-if="chartOptions && series" :options="chartOptions" :series="series" type="line"></apexchart>
    </div>
  </template>
  
  <script>
  import * as XLSX from 'xlsx';
  import { ref } from 'vue';
  
  export default {
    data() {
      return {
        selectedYear: null,
        chartOptions: null,
        series: null
      };
    },
    computed: {
      availableYears() {
        const years = new Set();
        this.excelData.forEach(row => {
          const date = new Date(row.date); // Assumi che 'date' sia il nome della colonna contenente la data
          years.add(date.getFullYear());
        });
        return Array.from(years);
      }
    },
    methods: {
      async loadExcelData() {
        try {
          const response = await fetch('/dati1.xlsx'); // Percorso del file Excel
          const arrayBuffer = await response.arrayBuffer();
          const data = new Uint8Array(arrayBuffer);
          const workbook = XLSX.read(data, { type: 'array' });
          const worksheet = workbook.Sheets[workbook.SheetNames[0]];
  
          const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, defval: '' });
          this.excelData = jsonData;
        } catch (error) {
          console.error('Errore nel caricamento dei dati Excel:', error);
        }
      },
      updateChart() {
        if (this.selectedYear) {
          const filteredData = this.excelData.filter(row => {
            const date = new Date(row.date);
            return date.getFullYear() === this.selectedYear;
          });
          // Crea il grafico basato sui dati filtrati
          this.createChart(filteredData);
        }
      },
      createChart(data) {
        // Implementa la logica per creare il grafico basato sui dati forniti
      }
    },
    mounted() {
      this.loadExcelData();
    }
  };
  </script>
  
  <style scoped>
  /* Stile opzionale per il componente */
  </style>
  