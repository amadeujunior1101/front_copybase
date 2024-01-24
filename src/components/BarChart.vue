<template>
  <div>
    <canvas ref="myChart"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';
import ChartDataLabels from 'chartjs-plugin-datalabels';

export default {
  props: {
    labels: {
      type: Array,
      required: true,
    },
    data: {
      type: Array,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
    borderColor: {
      type: String,
      required: true,
    },
    borderWidth: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      myChart: null,
    };
  },
  mounted() {
    this.renderChart();
  },
  methods: {
    renderChart() {
      const ctx = this.$refs.myChart.getContext('2d');
      this.myChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: this.labels,
          datasets: [{
            label: this.label,
            data: this.data,
            backgroundColor: ['#45bb4f', '#8d2031'],
            borderColor: this.borderColor,
            borderWidth: this.borderWidth,
          }],
        },
        plugins: [ChartDataLabels],
        options: {
          plugins: {
            datalabels: {
              anchor: 'center',
              align: 'center',
              color: '#000',
              font: {
                weight: 'bold',
              },
              formatter: (value, context) => {
                const isFirstValueString = context.dataset.data.indexOf(value) === 0 && typeof value === 'string';

                if (isFirstValueString) {
                  return parseFloat(value).toLocaleString('pt-BR', {
                    style: 'currency',
                    currency: 'BRL',
                    minimumFractionDigits: 2,
                    maximumFractionDigits: 2,
                  });
                }
                return value;
              },
            },
          },
          scales: {
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    },
    updateChart(newData) {
      if (this.myChart) {
        this.myChart.data.datasets[0].data = newData;
        this.myChart.update();
      }
    },
  },
};
</script>

<style scoped>
</style>
