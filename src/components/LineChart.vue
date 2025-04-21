<script setup lang="ts">
import { ref, onMounted, onUnmounted, inject } from 'vue';
import * as echarts from 'echarts';
import type { EChartsOption } from 'echarts';

const chartDom = ref<HTMLDivElement | null>(null);
const myChart = ref<echarts.ECharts | null>(null);
const option: EChartsOption = {
  xAxis: {
    type: 'category',
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
  },
  yAxis: {
    type: 'value',
  },
  series: [
    {
      data: [150, 230, 224, 218, 135, 147, 260],
      type: 'line',
    },
  ],
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'line',
    },
  },
};

const chartInstances = inject<any[]>('chartInstances', []);
const dispatchAction = inject<(params: any) => void>('dispatchAction', () => {});

onMounted(() => {
  if (!chartDom.value) return;

  myChart.value = echarts.init(chartDom.value);
  myChart.value.setOption(option);

  // 차트 인스턴스 저장
  if (myChart.value) {
    chartInstances.push(myChart.value);
  }

  // 마우스 이벤트 처리
  myChart.value.on('mouseover', params => {
    dispatchAction({
      from: myChart.value,
      seriesIndex: params.seriesIndex,
      dataIndex: params.dataIndex,
    });
  });

  // 마우스 나갔을 때 이벤트 처리
  myChart.value.on('mouseout', () => {
    chartInstances.forEach(chart => {
      if (chart && chart !== myChart.value) {
        chart.dispatchAction({
          type: 'hideTip',
        });
      }
    });
  });
});

onUnmounted(() => {
  if (myChart.value) {
    // 차트 인스턴스 제거
    const index = chartInstances.indexOf(myChart.value);
    if (index > -1) {
      chartInstances.splice(index, 1);
    }
    myChart.value.dispose();
  }
});
</script>

<template>
  <div ref="chartDom" style="width: 600px; height: 400px"></div>
</template>
