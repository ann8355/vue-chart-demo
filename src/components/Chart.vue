<template>
  <bar :options="chartOptions" :data="chartData" />
  <!-- <line :data="chartData" :options="chartOptions" /> -->
</template>

<script setup>
import { Bar, Line } from 'vue-chartjs'
import {
  BarElement,
  CategoryScale,
  Chart as ChartJS,
  Legend,
  LineController,
  LineElement,
  LinearScale,
  PointElement,
  Title,
  Tooltip,
} from 'chart.js'

const props = defineProps({
  barData: {
    type: Array,
    default: () => [],
  },
  lineData: {
    type: Array,
    default: () => [],
  },
  xLabels: {
    type: Array,
    default: () => [],
  },
  barYLabels: {
    type: String,
    default: () => '億元',
  },
  barLabel: {
    type: String,
    default: () => '營業額',
  },
  barColor: {
    type: String,
    default: () => '#ecea00',
  },
  barHoverColor: {
    type: String,
    default: () => '#dcda00',
  },
  lineLabel: {
    type: String,
    default: () => '年增率',
  },
  lineYLabels: {
    type: String,
    default: () => '%',
  },
  lineColor: {
    type: String,
    default: () => '#f87979',
  },
  linePointStyle: {
    type: String,
    default: () => 'circle', // 'triangle'
  },
  title: {
    type: String,
    default: () => '',
  },
  legendTitle: {
    type: String,
    default: () => '',
  },
})
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  LineController,
)
const chartData = ref({
  datasets: [],
  labels: props.xLabels,
})
const chartOptions = ref({
  responsive: true,
  // maintainAspectRatio: false,
  animation: {
    duration: 500,
  },
  transitions: {
    show: {
      animations: {
        x: {
          // from: 0,
        },
        y: {
          // from: 0,
        },
      },
    },
    hide: {
      animations: {
        x: {
          // to: 0,
        },
        y: {
          // to: 0,
        },
      },
    },
  },
  scales: {
    // grid: {}, //調整網格樣式
    y: {
      title: {
        display: props.barYLabels,
        text: props.barYLabels,
        align: 'end',
        // font: {
        //   size: 16,
        // },
      },
      display: props.barData.length > 0,
      position: 'left',
    },
    y1: {
      title: {
        display: props.lineYLabels,
        text: props.lineYLabels,
        align: 'end',
        // font: {
        //   size: 16,
        // },
      },
      display: props.lineData.length > 0,
      position: props.barData.length > 0 && props.lineData.length > 0 ? 'right' : 'left',
      grid: {
        drawOnChartArea: false, // only want the grid lines for one axis to show up
      },
    },
  },
  interaction: {
    mode: 'index',
  },
  plugins: {
    tooltip: {
      backgroundColor: 'rgba(255, 255, 255, 0.8)',
      borderColor: props.lineColor,
      borderWidth: 1,
      xAlign: 'center',
      cornerRadius: 3,
      bodyFont: {
        size: 16,
      },
      titleFont: {
        size: 16,
      },
      bodyColor: '#000',
      titleColor: '#000',
      usePointStyle: true,
      boxWidth: 8,
      boxHeight: 8,
      boxPadding: 5,
      callbacks: {
        label: context => {
          console.log(context)
          return `${context?.dataset.label} : ${context?.raw}${context?.dataset.type === 'line' ? '%' : '元'}`
        },
      },
      // displayColors: false,
    },
    title: {
      display: true,
      text: props.title,
      color: '#000',
      font: {
        size: 24,
      },
    },
    legend: {
      onHover: (evt, item, legend) => {
        legend.chart.data.datasets.forEach(e => {
          if (e.backgroundColor.includes(item.fillStyle)) {
            e.backgroundColor += '4D'
            e.borderColor += '4D'
          }
        })
        legend.chart.update()
      },
      onLeave: (evt, item, legend) => {
        legend.chart.data.datasets.forEach(e => {
          if (e.backgroundColor.includes('4D')) {
            if (e.backgroundColor.includes(item.fillStyle)) {
              e.backgroundColor = e.backgroundColor.slice(0, -2)
              e.borderColor = e.borderColor.slice(0, -2)
            }
          }
        })
        legend.chart.update()
      },
      align: 'end',
      labels: {
        usePointStyle: true,
        font: {
          weight: 'bolder',
          size: 18,
        },
      },
      title: {
        display: props.legendTitle,
        text: props.legendTitle,
        font: {
          weight: 'bolder',
          size: 16,
        },
      },
    },
  },
})
watchEffect(() => {
  if (props.barData.length > 0) {
    chartData.value.datasets.push({
      type: 'bar',
      label: props.barLabel,
      data: props.barData,
      backgroundColor: props.barColor,
      hoverBackgroundColor: props.barHoverColor,
      borderColor: props.barColor,
      order: 2,
      barPercentage: 0.5,
      yAxisID: 'y',
    })
  }
  if (props.lineData.length > 0) {
    chartData.value.datasets.push({
      type: 'line',
      label: props.lineLabel,
      data: props.lineData,
      backgroundColor: props.lineColor,
      borderColor: props.lineColor,
      fill: false,
      order: 1,
      pointStyle: props.linePointStyle,
      radius: 12,
      hoverRadius: 20,
      yAxisID: 'y1',
    })
  }
})
</script>
