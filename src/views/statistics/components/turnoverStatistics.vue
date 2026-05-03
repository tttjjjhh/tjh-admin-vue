<template>
  <InkCard class="chart-card">
    <h2 class="homeTitle">营业额统计</h2>
    <div class="charBox">
      <div id="main" style="width: 100%; height: 320px"></div>
      <ul class="orderListLine turnover">
        <li>营业额(元)</li>
      </ul>
    </div>
  </InkCard>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from 'vue-property-decorator'
import * as echarts from 'echarts'
import InkCard from '@/components/ink/InkCard.vue'
import { inkAxisStyle, inkTooltip, inkLegend } from '@/utils/inkEcharts'
@Component({
  name: 'TurnoverStatistics',
  components: { InkCard },
})
export default class extends Vue {
  @Prop() private turnoverdata!: any
  @Watch('turnoverdata')
  getData() {
    this.$nextTick(() => {
      this.initChart()
    })
  }
  initChart() {
    type EChartsOption = echarts.EChartsOption
    const chartDom = document.getElementById('main') as any
    const myChart = echarts.init(chartDom)

    var option: any
    option = {
      // title: {
      //   text: '营业额(元)',
      //   top: 'bottom',
      //   left: 'center',
      //   textAlign: 'center',
      //   textStyle: {
      //     fontSize: 12,
      //     fontWeight: 'normal',
      //   },
      // },
      tooltip: inkTooltip,
      grid: {
        top: '5%',
        left: '10',
        right: '50',
        bottom: '12%',
        containLabel: true,
      },
      xAxis: {
        type: 'category',
        boundaryGap: false,
        axisLabel: inkAxisStyle.axisLabel,
        axisLine: inkAxisStyle.axisLine,
        data: this.turnoverdata.dateList, //后端传来的动态数据
      },
      yAxis: [
        {
          type: 'value',
          min: 0,
          //max: 50000,
          //interval: 1000,
          axisLabel: inkAxisStyle.axisLabel,
          splitLine: inkAxisStyle.splitLine
        }
      ],
      series: [
        {
          name: '营业额',
          type: 'line',
          // stack: 'Total',
          smooth: false, //否平滑曲线
          showSymbol: false, //未显示鼠标上移的圆点
          symbolSize: 10,
          // symbol:"circle", //设置折线点定位实心点
          itemStyle: {
            normal: {
              color: '#2F4858',
              lineStyle: {
                color: '#B08A4D',
              },
            },
            emphasis: {
              color: '#fff',
              borderWidth: 5,
              borderColor: '#FFC100',
            },
          },

          data: this.turnoverdata.turnoverList,
        },
      ],
    }
    option && myChart.setOption(option)
  }
}
</script>
