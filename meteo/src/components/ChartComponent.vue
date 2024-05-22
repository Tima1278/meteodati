<template>
    <div>
      <input type="file" @change="handleFileUpload" />
      <apexchart type="line" :options="chartOptions" :series="series" />
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
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
          }
        },
        series: []
      };
    },
    methods: {
      async handleFileUpload(event) {
        const file = event.target.files[0];
        if (file) {
          const data = await file.arrayBuffer();
          const workbook = read(data);
          const sheetName = workbook.SheetNames[0];
          const sheet = workbook.Sheets[sheetName];
          const json = utils.sheet_to_json(sheet, { header: 1 });
          this.processData(json);
        }
      },
      processData(data) {
        const categories = data[0];
        const seriesData = data.slice(1).map(row => ({
          name: row[0],
          data: row.slice(1)
        }));
  
        this.chartOptions.xaxis.categories = categories.slice(1);
        this.series = seriesData;
      }
    }
  };
  </script>
  
  <style>
  /* Stili opzionali */
  </style>
  