<template>
  <div class="table-container">
    <table class="table">
      <thead>
        <tr>
          <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, rowIndex) in excelData" :key="rowIndex" @mouseover="highlightRow(rowIndex)" @mouseleave="unhighlightRow(rowIndex)">
          <td v-for="(cell, colIndex) in row" :key="colIndex">{{ cell }}</td>
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
    const excelData = ref([]);
    const headers = ref([]);
    const highlightedRow = ref(null);

    const loadExcelFile = async () => {
      try {
        const response = await fetch('/datiufficiali.xlsx');
        const arrayBuffer = await response.arrayBuffer();
        const data = new Uint8Array(arrayBuffer);
        const workbook = XLSX.read(data, { type: 'array' });
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];

        // Estrai intestazioni e dati
        const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, defval: '' });
        headers.value = jsonData[0];
        excelData.value = jsonData.slice(1);

        // Tratta le celle unite
        const mergeCells = worksheet['!merges'] || [];
        mergeCells.forEach(mergeCell => {
          const { s, e } = mergeCell;
          const startRowIndex = s.r;
          const startColIndex = s.c;
          const endRowIndex = e.r;
          const endColIndex = e.c;

          // Unisci celle nell'array excelData
          for (let i = startRowIndex; i <= endRowIndex; i++) {
            for (let j = startColIndex; j <= endColIndex; j++) {
              if (i !== startRowIndex || j !== startColIndex) {
                excelData.value[i][j] = excelData.value[startRowIndex][startColIndex];
              }
            }
          }
        });
      } catch (error) {
        console.error('Errore nel caricamento del file Excel:', error);
      }
    };

    const highlightRow = (index) => {
      highlightedRow.value = index;
    };

    const unhighlightRow = () => {
      highlightedRow.value = null;
    };

    onMounted(() => {
      loadExcelFile();
    });

    return {
      excelData,
      headers,
      highlightRow,
      unhighlightRow,
      highlightedRow,
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
  border: 1px solid #ddd; /* Aggiunto bordo */
}

th, td {
  border: 1px solid #ddd;
  padding: 8px; /* Ridotto il padding */
  text-align: left;
}

th {
  background-color: #f2f2f2;
  color: #333;
  font-weight: bold;
  text-transform: uppercase;
}

tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

tbody tr:hover {
  background-color: #f2f2f2;
}

tbody tr:hover td {
  color: #333;
}

tbody tr.highlighted {
  background-color: #cce5ff;
}

tbody tr.highlighted td {
  color: #000;
}

</style>
