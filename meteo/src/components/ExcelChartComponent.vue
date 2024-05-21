<template>
    <div>
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
        chartData: null
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
          const categories = data.slice(1).map(row => row[0]);
          const seriesData = data.slice(1).map(row => row[1]);
  
          // Remove any undefined values
          const filteredCategories = categories.filter(item => item !== undefined);
          const filteredSeriesData = seriesData.filter(item => item !== undefined);
  
          this.chartOptions.xaxis.categories = filteredCategories;
          this.chartData = {
            series: [{
              name: 'Serie 1',
              data: filteredSeriesData
            }]
          };
        } else {
          console.error("Invalid data format in Excel file");
        }
      }
    }
  };
  </script>
  
  <style scoped>
  /* Aggiungi eventuali stili per il tuo componente */
  </style>
  