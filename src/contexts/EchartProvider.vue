<script setup lang="ts">
import { provide, ref } from 'vue';

const chartInstances = ref<any[]>([]);

provide('chartGroup', 'my-chart-group');
provide('chartInstances', chartInstances.value);

const dispatchAction = (params: { from: any; seriesIndex: number; dataIndex: number }) => {
  chartInstances.value.forEach(chart => {
    if (chart) {
      chart.dispatchAction({
        type: 'showTip',
        seriesIndex: params.seriesIndex,
        dataIndex: params.dataIndex,
      });
    }
  });
};

provide('dispatchAction', dispatchAction);
</script>

<template>
  <slot />
</template>
