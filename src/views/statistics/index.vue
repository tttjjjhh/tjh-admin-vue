<template>
  <InkPage class="stats-page">
    <TitleIndex @sendTitleInd="getTitleNum" :flag="flag" :tateData="tateData" />
    <div class="grid two">
      <TurnoverStatistics :turnoverdata="turnoverData" />
      <UserStatistics :userdata="userData" />
    </div>
    <div class="grid two">
      <OrderStatistics :orderdata="orderData" :overviewData="overviewData" />
      <Top :top10data="top10Data" />
    </div>
  </InkPage>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { get1stAndToday, past7Day, past30Day, pastWeek, pastMonth } from '@/utils/formValidate'
import { getTurnoverStatistics, getUserStatistics, getOrderStatistics, getTop } from '@/api/index'
import TitleIndex from './components/titleIndex.vue'
import TurnoverStatistics from './components/turnoverStatistics.vue'
import UserStatistics from './components/userStatistics.vue'
import OrderStatistics from './components/orderStatistics.vue'
import Top from './components/top10.vue'
import { InkPage } from '@/components/ink'

@Component({
  name: 'Dashboard',
  components: { TitleIndex, TurnoverStatistics, UserStatistics, OrderStatistics, Top, InkPage },
})
export default class extends Vue {
  private overviewData = {} as any
  private flag = 2
  private tateData = []
  private turnoverData = {} as any
  private userData = {}
  private orderData = { data: {} } as any
  private top10Data = {}
  created() { this.getTitleNum(2) }
  init(begin: any, end: any) {
    this.$nextTick(() => {
      this.getTurnoverStatisticsData(begin, end)
      this.getUserStatisticsData(begin, end)
      this.getOrderStatisticsData(begin, end)
      this.getTopData(begin, end)
    })
  }
  async getTurnoverStatisticsData(begin: any, end: any) {
    const data = await getTurnoverStatistics({ begin, end })
    const d = data.data.data
    this.turnoverData = { dateList: d.dateList.split(','), turnoverList: d.turnoverList.split(',') }
  }
  async getUserStatisticsData(begin: any, end: any) {
    const data = await getUserStatistics({ begin, end })
    const d = data.data.data
    this.userData = { dateList: d.dateList.split(','), totalUserList: d.totalUserList.split(','), newUserList: d.newUserList.split(',') }
  }
  async getOrderStatisticsData(begin: any, end: any) {
    const data = await getOrderStatistics({ begin, end })
    const d = data.data.data
    this.orderData = { data: { dateList: d.dateList.split(','), orderCountList: d.orderCountList.split(','), validOrderCountList: d.validOrderCountList.split(',') }, totalOrderCount: d.totalOrderCount, validOrderCount: d.validOrderCount, orderCompletionRate: d.orderCompletionRate }
  }
  async getTopData(begin: any, end: any) {
    const data = await getTop({ begin, end })
    const d = data.data.data
    this.top10Data = { nameList: d.nameList.split(',').reverse(), numberList: d.numberList.split(',').reverse() }
  }
  getTitleNum(data) {
    switch (data) {
      case 1: this.tateData = get1stAndToday(); break
      case 2: this.tateData = past7Day(); break
      case 3: this.tateData = past30Day(); break
      case 4: this.tateData = pastWeek(); break
      case 5: this.tateData = pastMonth(); break
    }
    this.init(this.tateData[0], this.tateData[1])
  }
}
</script>

<style scoped>
.stats-page{position:relative}
.stats-page::before{content:'';position:absolute;inset:0;pointer-events:none;opacity:.16;background:linear-gradient(180deg,rgba(123,134,150,.12),rgba(123,134,150,0) 180px)}
.grid.two{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-top:14px}
</style>
