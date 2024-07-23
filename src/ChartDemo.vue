<template>
  <wrap-layout :show-group-menu="false">
    <chart :bar-data="barData" :x-labels="xLabels" :line-data="lineData" :title="title"></chart>
  </wrap-layout>
</template>

<script setup>
import WrapLayout from './components/WrapLayout.vue'
import Chart from './components/Chart.vue'
// import { useInit } from '@/api/'

// const { isSuccess, data } = useInit()
const title = ref('固定資產(不含土地)增購金額')
const barData = ref([100, 200, 300, 400])
const xLabels = ref(['113年1季', '113年2季', '113年3季', '113年4季'])
const lineData = ref([4, 15, 57, 88]) // [{x: '2016-12-25', y: 20}, {x: '2016-12-26', y: 10}]
// watchEffect(() => {
//   if (isSuccess.value) {
//     barData.value = data.value
//   }
// })
</script>
