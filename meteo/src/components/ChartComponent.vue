<template>
    <div>
      <apexchart type="line" :options="chartOptions" :series="series"></apexchart>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        chartOptions: {
          chart: {
            id: 'vuechart-example'
          },
          xaxis: {
            categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998, 1999]
          }
        },
        series: [{
          name: 'serie-1',
          data: [30, 40, 35, 50, 49, 60, 70, 91, 125]
        }]
      }
    }
  }
  </script>
  
  <style scoped>
  /* Aggiungi eventuali stili per il tuo componente */
  </style>
  