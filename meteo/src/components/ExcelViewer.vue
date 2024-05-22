<template>
    <div>
      <table v-if="excelData.length">
        <thead>
          <tr>
            <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in excelData" :key="rowIndex">
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
  
      const loadExcelFile = async () => {
        const response = await fetch('/datiufficiali.xlsx');
        const arrayBuffer = await response.arrayBuffer();
        const data = new Uint8Array(arrayBuffer);
        const workbook = XLSX.read(data, { type: 'array' });
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, defval: '' });
  
        // Estrai intestazioni e dati
        headers.value = jsonData[0];
        excelData.value = jsonData.slice(1);
      };
  
      onMounted(() => {
        loadExcelFile();
      });
  
      return {
        excelData,
        headers,
      };
    },
  };
  </script>
  
  <style scoped>
  table {
    width: 100%;
    border-collapse: collapse;
    font-family: Arial, sans-serif;
    border: 1px solid #ddd;
  }
  
  th, td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
  }
  
  th {
    background-color: #f2f2f2;
    color: #333;
  }
  
  tbody tr:nth-child(even) {
    background-color: #f9f9f9;
  }
  
  tbody tr:hover {
    background-color: #f2f2f2;
  }
  </style>
  