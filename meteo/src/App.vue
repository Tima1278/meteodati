<template>
  <div id="app">
    <NavigationBar @navigate="navigateToSection" />
    <div class="main-content">
      <ExcelViewer v-if="currentSection === 'Excel'" />
      <PaginaGrafici v-else-if="currentSection === 'Grafici'" />
      <HottestCities v-else-if="currentSection === 'Città più calde'" />
      <GestioneDatiCitta v-else-if="currentSection === 'Crea'" @add-data="addCityData" />
    </div>
  </div>
</template>

<script>
import NavigationBar from './components/NavigationBar.vue';
import ExcelViewer from './components/ExcelViewer.vue';
import PaginaGrafici from './components/PaginaGrafici.vue';
import HottestCities from './components/HottestCities.vue';

export default {
  name: 'App',
  components: {
    NavigationBar,
    ExcelViewer,
    PaginaGrafici,
    HottestCities,
  },
  data() {
    return {
      currentSection: 'Excel',
      datiCitta: [] // Inizializza l'array per contenere i dati delle città
    };
  },
  methods: {
    navigateToSection(section) {
      this.currentSection = section;
    },
    addCityData(newData) {
      // Aggiungi i nuovi dati alla struttura dati globale
      this.datiCitta.push(newData);
    }
  }
};
</script>

<style>
/* Aggiungi eventuali stili qui */
</style>
