<template>
  <!-- Prima tabella -->
  <div class="table-container">
    <h2 class="table-title">Temperatura media</h2>
    <table class="table" v-if="tableData1.length">
      <thead>
        <tr>
          <th>Città</th>
          <th v-for="date in dates1" :key="date">{{ date }}</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(row, rowIndex) in tableData1"
          :key="rowIndex"
          @mouseover="highlightRow(rowIndex)"
          @mouseleave="unhighlightRow(rowIndex)"
          :class="{ highlighted: highlightedRow === rowIndex }"
        >
          <td>{{ row.citta }}</td>
          <td v-for="date in dates1" :key="date">{{ row.dati.find(data => data.data === date)?.valore || '' }}</td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- Seconda tabella -->
  <div class="table-container">
    <h2 class="table-title">Precipitazione totale</h2>
    <table class="table" v-if="tableData2.length">
      <thead>
        <tr>
          <th>Città</th>
          <th v-for="date in dates2" :key="date">{{ date }}</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(row, rowIndex) in tableData2"
          :key="rowIndex"
          @mouseover="highlightRow2(rowIndex)"
          @mouseleave="unhighlightRow2(rowIndex)"
          :class="{ highlighted: highlightedRow2 === rowIndex }"
        >
          <td>{{ row.citta }}</td>
          <td v-for="date in dates2" :key="date">{{ row.dati.find(data => data.data === date)?.valore || '' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const tableData1 = ref([]);
    const dates1 = ref([]);
    const highlightedRow = ref(null);

    const tableData2 = ref([]);
    const dates2 = ref([]);
    const highlightedRow2 = ref(null);

    const loadExcelFile = async () => {
      try {
        // Caricamento del primo file Excel
        const response1 = await fetch('/dati1.xlsx');
        const arrayBuffer1 = await response1.arrayBuffer();
        const data1 = new Uint8Array(arrayBuffer1);
        const workbook1 = XLSX.read(data1, { type: 'array' });
        const worksheet1 = workbook1.Sheets[workbook1.SheetNames[0]];

        // Estrazione dei dati dal primo file Excel
        const jsonData1 = XLSX.utils.sheet_to_json(worksheet1, { header: 1, defval: '' });

        // Estrazione delle date e costruzione del JSON per la prima tabella
        dates1.value = jsonData1[1].slice(1);
        const citiesData1 = jsonData1.slice(2).reduce((acc, row) => {
          const cityName = row[0];
          if (!acc[cityName]) {
            acc[cityName] = { citta: cityName, dati: [] };
          }
          dates1.value.forEach((date, index) => {
            acc[cityName].dati.push({
              data: date,
              valore: row[index + 1]
            });
          });
          return acc;
        }, {});
        tableData1.value = Object.values(citiesData1).sort((a, b) => a.citta.localeCompare(b.citta)); // Ordina le città per nome

        // Salvataggio dei dati nel localStorage
        localStorage.setItem('tableData1', JSON.stringify(tableData1.value));

        // Caricamento del secondo file Excel
        const response2 = await fetch('/dati2.xlsx');
        const arrayBuffer2 = await response2.arrayBuffer();
        const data2 = new Uint8Array(arrayBuffer2);
        const workbook2 = XLSX.read(data2, { type: 'array' });
        const worksheet2 = workbook2.Sheets[workbook2.SheetNames[0]];

        // Estrazione dei dati dal secondo file Excel
        const jsonData2 = XLSX.utils.sheet_to_json(worksheet2, { header: 1, defval: '' });

        // Estrazione delle date e costruzione del JSON per la seconda tabella
        dates2.value = jsonData2[1].slice(1);
        const citiesData2 = jsonData2.slice(2).reduce((acc, row) => {
          const cityName = row[0];
          if (!acc[cityName]) {
            acc[cityName] = { citta: cityName, dati: [] };
          }
          dates2.value.forEach((date, index) => {
            acc[cityName].dati.push({
              data: date,
              valore: row[index + 1]
            });
          });
          return acc;
        }, {});
        tableData2.value = Object.values(citiesData2).sort((a, b) => a.citta.localeCompare(b.citta)); // Ordina le città per nome

        // Salvataggio dei dati nel localStorage
        localStorage.setItem('tableData2', JSON.stringify(tableData2.value));

      } catch (error) {
        console.error('Errore nel caricamento dei file Excel:', error);
      }
    };

    const updateTablesFromLocalStorage = () => {
      const storedTableData1 = localStorage.getItem('tableData1');
      const storedTableData2 = localStorage.getItem('tableData2');

      if (storedTableData1) {
        tableData1.value = JSON.parse(storedTableData1);
      }

      if (storedTableData2) {
        tableData2.value = JSON.parse(storedTableData2);
      }
    };

    const highlightRow = (index) => {
      highlightedRow.value = index;
    };

    const unhighlightRow = () => {
      highlightedRow.value = null;
    };

    const highlightRow2 = (index) => {
      highlightedRow2.value = index;
    };

    const unhighlightRow2 = () => {
      highlightedRow2.value = null;
    };

    onMounted(() => {
      loadExcelFile();
      updateTablesFromLocalStorage();
    });

    return {
      tableData1,
      dates1,
      highlightRow,
      unhighlightRow,
      highlightedRow,
      tableData2,
      dates2,
      highlightRow2,
      unhighlightRow2,
      highlightedRow2,
    };
  },
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
