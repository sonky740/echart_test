<script setup lang="ts">
import LineChart from './components/LineChart.vue';
import EchartProvider from './contexts/EchartProvider.vue';
</script>

<template>
  <div>
    <EchartProvider>
      <LineChart />
      <LineChart />
    </EchartProvider>
  </div>
</template>

<style scoped></style>
