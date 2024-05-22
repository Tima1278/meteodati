<template>
  <div id="app">
    <Navbar @navigate="navigateToSection" />
    <div class="main-content">
      <ExcelViewer v-if="currentSection === 'Excel'" />
      <!-- Aggiungi altri componenti per le altre sezioni -->
    </div>
  </div>
</template>

<script>
import Navbar from './components/NavigationBar.vue';
import ExcelViewer from './components/ExcelViewer.vue';

export default {
  name: 'App',
  components: {
    Navbar,
    ExcelViewer
  },
  data() {
    return {
      currentSection: 'Excel' // Imposta la sezione corrente su Excel
    };
  },
  methods: {
    navigateToSection(section) {
      this.currentSection = section;
    }
  }
};
</script>

<style>
/* Aggiungi eventuali stili qui */
</style>
