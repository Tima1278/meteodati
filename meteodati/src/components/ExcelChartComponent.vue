<template>
  <div>
    <div id="chart-container" style="overflow-x: auto;">
      <apexchart v-if="chartData && chartData.series && chartData.series.length > 0" type="line" :options="chartOptions" :series="chartData.series"></apexchart>
    </div>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';
import VueApexCharts from 'vue3-apexcharts';

export default {
  components: {
    apexchart: VueApexCharts
  },
  data() {
    return {
      chartOptions: {
        chart: {
          id: 'vuechart-example'
        },
        xaxis: {
          categories: [],
          labels: {
            style: {
              fontSize: '15px'
            },
            rotateAlways: true,
            rotate: 0
          }
        },
        yaxis: {
          labels: {
            style: {
              fontSize: '15px'
            }
          }
        },
        stroke: {
          width: 2
        },
        tooltip: {
          y: {
            formatter: function (val) {
              return val + "°C";
            }
          }
        }
      },
      chartData: null
    };
  },
  mounted() {
    this.loadExcelFile();
  },
  methods: {
    async loadExcelFile() {
      try {
        const response = await fetch('/data.xlsx');
        const arrayBuffer = await response.arrayBuffer();
        const data = new Uint8Array(arrayBuffer);
        const workbook = XLSX.read(data, { type: 'array' });
        const firstSheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[firstSheetName];
        const jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });

        console.log("JSON data:", jsonData);

        const years = jsonData[0].slice(1);
        const cities = jsonData.slice(1).map(row => row[0]);
        const seriesData = jsonData.slice(1).map(row => row.slice(1));

        this.chartOptions.xaxis.categories = years;
        this.chartData = {
          series: seriesData.map((data, index) => {
            return {
              name: cities[index],
              data: data.map(temp => {
                const temperature = parseFloat(temp);
                return temperature > 60 ? null : temperature;
              })
            };
          })
        };
      } catch (error) {
        console.error("Error loading or processing the Excel file:", error);
      }
    }
  }
};
</script>

<style scoped>
#chart-container {
  width: 100%;
  height: 100%;
}
</style>
