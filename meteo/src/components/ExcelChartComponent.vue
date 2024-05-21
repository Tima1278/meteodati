<template>
    <div>
      <input type="date" v-model="selectedDate" @input="updateChartData" :disabledDates="disabledDates">
      <apexchart v-if="chartData && chartData.series && chartData.series.length > 0" type="line" :options="chartOptions" :series="chartData.series"></apexchart>
    </div>
  </template>
  
  <script>
  import * as XLSX from 'xlsx';
  import VueApexCharts from 'vue3-apexcharts';
  
  export default {
    components: {
      apexchart: VueApexCharts
    },
    data() {
      return {
        chartOptions: {
          chart: {
            id: 'vuechart-example'
          },
          xaxis: {
            categories: []
          }
        },
        chartData: null,
        selectedDate: null,
        availableDates: [] // New data to store available dates
      };
    },
    mounted() {
      this.loadExcelFile();
    },
    methods: {
      async loadExcelFile() {
        try {
          const response = await fetch('/data.xlsx');
          const arrayBuffer = await response.arrayBuffer();
          const data = new Uint8Array(arrayBuffer);
          const workbook = XLSX.read(data, { type: 'array' });
          const firstSheetName = workbook.SheetNames[0];
          const worksheet = workbook.Sheets[firstSheetName];
          const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
  
          this.processData(jsonData);
        } catch (error) {
          console.error("Error loading or processing the Excel file:", error);
        }
      },
      processData(data) {
        // Check if data is valid and has the correct structure
        if (data.length > 1) {
          const dates = data.slice(1).map(row => row[0]); // Date column
          const temperatures = data.slice(1).map(row => parseFloat(row[1])); // Temperature column
  
          // Filter temperatures less than 60
          const filteredTemperatures = temperatures.filter(temp => temp < 60);
  
          this.availableDates = dates; // Store available dates
          this.chartOptions.xaxis.categories = dates;
          this.chartData = {
            series: [{
              name: 'Temperature',
              data: filteredTemperatures
            }]
          };
        } else {
          console.error("Invalid data format in Excel file");
        }
      },
      updateChartData() {
        if (this.selectedDate && this.chartData) {
          const selectedDataIndex = this.chartOptions.xaxis.categories.indexOf(this.selectedDate);
          const selectedTemperature = this.chartData.series[0].data[selectedDataIndex];
  
          this.chartData = {
            series: [{
              name: 'Temperature',
              data: [selectedTemperature]
            }]
          };
        }
      },
      disabledDates(date) {
        return !this.availableDates.includes(date);
      }
    }
  };
  </script>
  
  <style scoped>
  /* Aggiungi eventuali stili per il tuo componente */
  </style>
  