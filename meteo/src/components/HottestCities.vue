<template>
  <div class="container text-center">
    <h1>Top 10 città più calde</h1>
    <table class="table">
      <thead>
        <tr>
          <th>Città</th>
          <th>Temperatura Media (°C)</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(city, index) in hottestCities" :key="index">
          <td>{{ city.citta }}</td>
          <td>{{ city.temperaturaMedia.toFixed(2) }}</td>
        </tr>
      </tbody>
    </table>
    <div id="chart">
      <apexchart type="bar" :options="chartOptions" :series="series" />
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
      hottestCities: [],
      chartOptions: {
        chart: {
          id: 'temperature-trend',
          width: '100%',
          height: '300px', // Ridotto l'altezza del grafico
          toolbar: {
            show: false
          }
        },
        xaxis: {
          categories: [],
          title: {
            text: 'Città'
          }
        },
        yaxis: {
          title: {
            text: 'Temperatura Media (°C)'
          }
        },
        title: {
          text: 'Andamento delle temperature delle città più calde',
          align: 'center'
        }
      },
      series: []
    };
  },
  mounted() {
    this.loadHottestCitiesFromLocalStorage();
  },
  methods: {
    loadHottestCitiesFromLocalStorage() {
      try {
        const localStorageData = localStorage.getItem('tableData1');

        if (localStorageData) {
          const allData = JSON.parse(localStorageData);

          // Calcola la temperatura media per ogni città
          const cityAverages = allData.map(entry => {
            const temperatures = entry.dati.map(data => data.valore);
            const validTemperatures = temperatures.filter(temp => !isNaN(temp));
            if (validTemperatures.length > 0) {
              const averageTemperature = validTemperatures.reduce((acc, curr) => acc + curr, 0) / validTemperatures.length;
              return { citta: entry.citta, temperaturaMedia: parseFloat(averageTemperature.toFixed(2)) }; // Approssima a 2 cifre decimali
            }
            return null;
          }).filter(city => city !== null);

          // Ordina le città per temperatura media decrescente
          cityAverages.sort((a, b) => b.temperaturaMedia - a.temperaturaMedia);

          // Seleziona solo le prime 10 città più calde
          this.hottestCities = cityAverages.slice(0, 10);

          // Prepara i dati per il grafico
          this.chartOptions.xaxis.categories = this.hottestCities.map(city => city.citta);
          this.series = [{ name: 'Temperature', data: this.hottestCities.map(city => city.temperaturaMedia) }];
        } else {
          console.error('Dati non trovati nel localStorage.');
        }
      } catch (error) {
        console.error('Errore nel caricamento dei dati dal localStorage:', error);
      }
    }
  }
};
</script>

<style scoped>
.container {
  text-align: center;
}

.table {
  width: 100%;
  border-collapse: collapse;
  font-family: Arial, sans-serif;
  border: 1px solid #ddd;
}

th, td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: left;
}

th {
  background-color: #4CAF50; /* Verde */
  color: white;
  font-weight: bold;
  text-transform: uppercase;
}

tbody tr:nth-child(even) {
  background-color: #f2f2f2; /* Grigio chiaro */
}

tbody tr:hover {
  background-color: #dddddd; /* Grigio più scuro al passaggio del mouse */
}

#chart {
  margin-top: 20px;
  max-width: 600px; /* Larghezza massima per il grafico */
  margin: 0 auto;   /* Centra il grafico */
}
</style>
