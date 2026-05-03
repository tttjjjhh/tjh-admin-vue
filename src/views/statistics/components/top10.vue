<template>
  <InkCard class="chart-card top10">
    <h2 class="homeTitle">销量排名TOP10</h2>
    <div v-if="hasData" class="charBox">
      <div id="top" style="width: 100%; height: 380px"></div>
    </div>
    <InkEmptyState v-else title="暂无销量排行" description="山水静候，待佳肴上榜" />
  </InkCard>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from 'vue-property-decorator'
import * as echarts from 'echarts'
import InkCard from '@/components/ink/InkCard.vue'
import InkEmptyState from '@/components/ink/InkEmptyState.vue'
import { inkTooltip } from '@/utils/inkEcharts'
@Component({
  name: 'Top',
  components: { InkCard, InkEmptyState },
})
export default class extends Vue {
  @Prop() private top10data!: any
  get hasData() {
    return this.top10data && this.top10data.nameList && this.top10data.nameList.length
  }
  @Watch('top10data')
  getData() {
    if (!this.hasData) return
    this.$nextTick(() => this.initChart())
  }
  initChart() {
    const chartDom = document.getElementById('top') as any
    if (!chartDom) return
    const myChart = echarts.init(chartDom)
    const option: any = {
      tooltip: inkTooltip,
      grid: { top: '6%', left: '0', right: '0', bottom: '0', containLabel: true },
      xAxis: { show: false },
      yAxis: {
        axisLine: { show: false },
        axisTick: { show: false, alignWithLabel: true },
        type: 'category',
        axisLabel: { color: '#66707d', fontSize: 12 },
        data: this.top10data.nameList,
      },
      series: [{
        data: this.top10data.numberList,
        type: 'bar',
        showBackground: true,
        backgroundStyle: { color: 'rgba(102,112,125,.08)' },
        barWidth: 18,
        itemStyle: {
          barBorderRadius: [0, 9, 9, 0],
          color: new echarts.graphic.LinearGradient(1, 0, 0, 0, [
            { offset: 0, color: '#2F4858' },
            { offset: 1, color: '#B08A4D' },
          ]),
        },
      }],
    }
    myChart.setOption(option)
  }
}
</script>
