<template>
    <div class="container text-center">
      <h1>Top 10 città piu calde</h1>
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
    </div>
  </template>
  
  
  <script>
  export default {
    data() {
      return {
        hottestCities: []
      };
    },
    mounted() {
      this.findHottestCities();
    },
    methods: {
      findHottestCities() {
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
                return { citta: entry.citta, temperaturaMedia: averageTemperature };
              }
              return null;
            }).filter(city => city !== null);
  
            // Ordina le città per temperatura media decrescente
            cityAverages.sort((a, b) => b.temperaturaMedia - a.temperaturaMedia);
  
            // Seleziona solo le prime 10 città più calde
            this.hottestCities = cityAverages.slice(0, 10);
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
.table-container {
  max-width: 100%;
  overflow-x: auto;
}

.table {
  width: 100%;
  border-collapse: collapse;
  font-family: Arial, sans-serif;
  border: 1px solid #ddd;
}
.table-container {
  margin-bottom: 20px; /* Aggiunto spazio sotto la tabella */
  text-align: center; /* Centra il testo */
}

.table-title {
  margin-bottom: 10px; /* Aggiunto spazio sotto il titolo */
  font-size: 30px; /* Aumentata ulteriormente la dimensione del testo */
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

tbody tr.highlighted {
  background-color: #87CEEB; /* Azzurro chiaro */
}

tbody tr.highlighted td {
  color: #333; /* Testo più scuro per la riga evidenziata */
}
</style>
