<template>
    <div>
      <label for="city-input">Città:</label>
      <input type="text" id="city-input" v-model="newCityData.city" placeholder="Inserisci il nome della città">
      <div v-for="(yearData, year) in newCityData.data" :key="year">
        <label>{{ year }}</label>
        <input type="number" v-model="newCityData.data[year].temperature" placeholder="Temperatura">
        <!-- Rimuovi la variabile precipitation -->
      </div>
      <button @click="addCityData">Aggiungi dati città</button>
    </div>
</template>

<script>
export default {
  data() {
    return {
      newCityData: {
        city: '',
        data: Object.fromEntries(
          Array.from({ length: 16 }, (_, index) => [
            2006 + index,
            { temperature: null } // Rimuovi la variabile precipitation
          ])
        )
      }
    };
  },
  methods: {
    addCityData() {
      // Controlla se tutti i campi sono stati inseriti
      if (!this.newCityData.city) {
        alert('Inserisci il nome della città!');
        return;
      }

      // Verifica se sono stati inseriti i dati per ogni anno
      for (const year of Object.keys(this.newCityData.data)) {
        if (!this.newCityData.data[year].temperature) {
          alert(`Inserisci i dati per l'anno ${year}!`);
          return;
        }
      }

      // Costruisci il formato JSON richiesto
      const cityData = {
        citta: this.newCityData.city,
        dati: Object.entries(this.newCityData.data).map(([year, { temperature }]) => ({ // Rimuovi la variabile precipitation
          data: parseInt(year),
          valore: temperature
        }))
      };

      // Aggiungi i nuovi dati alla tabella corretta (assumendo che isTemperatureData sia una variabile booleana)
      if (this.isTemperatureData) {
        this.tableData1.push(cityData);
      } else {
        this.tableData2.push(cityData);
      }

      // Salva i dati aggiornati nel localStorage
      localStorage.setItem('tableData1', JSON.stringify(this.tableData1));
      localStorage.setItem('tableData2', JSON.stringify(this.tableData2));

      // Resetta i campi per l'aggiunta di nuovi dati
      this.newCityData = {
        city: '',
        data: Object.fromEntries(
          Array.from({ length: 16 }, (_, index) => [
            2006 + index,
            { temperature: null } // Rimuovi la variabile precipitation
          ])
        )
      };

      // Avvisa l'utente che i dati sono stati aggiunti con successo
      alert('Nuovi dati aggiunti con successo!');
    }
  }
};
</script>

<style scoped>
/* Stili specifici al componente qui */
</style>
