<!-- ChartComponent.vue -->
<template>
    <div id="chart">
      <apexchart type="line" height="350" :options="chartOptions" :series="series"></apexchart>
    </div>
  </template>
  
  <script>
  import { defineComponent } from 'vue';
  import { ApexChart } from 'vue3-apexcharts';
  
  export default defineComponent({
    name: 'ChartComponent',
    props: ['data'],
    components: {
      apexchart: ApexChart,
    },
    computed: {
      series() {
        return [{
          name: 'Dati',
          data: this.data.map(row => row[1]) // Assumiamo che il secondo elemento di ogni riga sia il valore da graficare
        }];
      },
      chartOptions() {
        return {
          xaxis: {
            categories: this.data.map(row => row[0]), // Assumiamo che il primo elemento di ogni riga sia l'etichetta dell'asse x
          },
          title: {
            text: 'Grafico dei Dati Excel',
          },
        };
      },
    },
  });
  </script>
  
  <style scoped>
  #chart {
    max-width: 600px;
    margin: auto;
  }
  </style>
  