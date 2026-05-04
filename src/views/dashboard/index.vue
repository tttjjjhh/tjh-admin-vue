<template>
  <div class="dashboard-container home ink-home-page">
    <!-- 营业数据 -->
    <div class="ink-home-module ink-home-module--overview">
      <Overview :overviewData="overviewData" />
    </div>
    <!-- end -->
    <!-- 订单管理 -->
    <div class="ink-home-module ink-home-module--order">
      <Orderview :orderviewData="orderviewData" />
    </div>
    <!-- end -->
    <div class="homeMain ink-home-grid">
      <!-- 菜品总览 -->
      <div class="ink-home-module ink-home-module--dish">
        <CuisineStatistics :dishesData="dishesData" />
      </div>
      <!-- end -->
      <!-- 套餐总览 -->
      <div class="ink-home-module ink-home-module--setmeal">
        <SetMealStatistics :setMealData="setMealData" />
      </div>
      <!-- end -->
    </div>
    <!-- 订单信息 -->
    <div class="ink-home-module ink-home-module--order-list">
      <OrderList
        :order-statics="orderStatics"
        @getOrderListBy3Status="getOrderListBy3Status"
      />
    </div>
    <!-- end -->
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import {
  getBusinessData,
  getDataOverView, //营业数据
  getOrderData, //订单管理今日订单
  getOverviewDishes, //菜品总览
  getSetMealStatistics, //套餐总览
} from '@/api/index'
import { getOrderListBy } from '@/api/order'
// 组件
// 营业数据
import Overview from './components/overview.vue'
// 订单管理
import Orderview from './components/orderview.vue'
// 菜品总览
import CuisineStatistics from './components/cuisineStatistics.vue'
// 套餐总览
import SetMealStatistics from './components/setMealStatistics.vue'
// 订单列表
import OrderList from './components/orderList.vue'
@Component({
  name: 'Dashboard',
  components: {
    Overview,
    Orderview,
    CuisineStatistics,
    SetMealStatistics,
    OrderList,
  },
})
export default class extends Vue {
  private todayData = {} as any
  private overviewData = {}
  private orderviewData = {} as any
  private flag = 2
  private tateData = []
  private dishesData = {} as any
  private setMealData = {}
  private orderListData = []
  private counts = 0
  private page: number = 1
  private pageSize: number = 10
  private status = 2
  private orderStatics = {} as any
  created() {
    this.init()
  }
  init() {
    this.$nextTick(() => {
      this.getBusinessData()
      this.getOrderStatisticsData()
      this.getOverStatisticsData()
      this.getSetMealStatisticsData()
    })
  }
  // 获取营业数据
  async getBusinessData() {
    const data = await getBusinessData()
    this.overviewData = data.data.data
  }
  // 获取今日订单
  async getOrderStatisticsData() {
    const data = await getOrderData()
    this.orderviewData = data.data.data
  }
  // 获取菜品总览数据
  async getOverStatisticsData() {
    const data = await getOverviewDishes()
    this.dishesData = data.data.data
  }
  // 获取套餐总览数据
  async getSetMealStatisticsData() {
    const data = await getSetMealStatistics()
    this.setMealData = data.data.data
  }
  //获取待处理，待派送，派送中数量
  getOrderListBy3Status() {
    getOrderListBy({})
      .then((res) => {
        if (res.data.code === 1) {
          this.orderStatics = res.data.data
        } else {
          this.$message.error(res.data.msg)
        }
      })
      .catch((err) => {
        this.$message.error('请求出错了：' + err.message)
      })
  }
}
</script>

<style lang="scss">
.dashboard-container.home {
  position: relative;
  padding: 12px 16px 20px;
  background: linear-gradient(180deg, #f7f5f0 0%, #f3f2ee 100%);
  min-height: calc(100vh - 60px);

  &::before {
    content: '';
    position: fixed;
    inset: 60px 0 0 190px;
    pointer-events: none;
    background-image: radial-gradient(rgba(0, 0, 0, 0.03) 1px, transparent 1px);
    background-size: 4px 4px;
    opacity: 0.25;
  }

  .container {
    background: rgba(255, 255, 255, 0.82);
    border: 1px solid #e8e2d6;
    border-radius: 12px;
    box-shadow: 0 4px 18px rgba(32, 36, 45, 0.05);
    margin-bottom: 14px;
    backdrop-filter: blur(1px);
  }

  .homeTitle {
    font-family: 'STKaiti', 'KaiTi', serif;
    font-size: 36px;
    color: #1f2a37;
    letter-spacing: 1px;
    span {
      color: #9f8354;
      font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
      font-size: 14px;
    }
    i {
      color: #7b8696;
    }
  }

  .overviewBox li,
  .orderviewBox li {
    border: 1px solid #ebe6dc;
    background: linear-gradient(180deg, #fff 0%, #fcfbf8 100%);
    border-radius: 10px;
  }

  .overviewBox .num,
  .orderviewBox .sumNum {
    color: #111827;
    font-family: 'Times New Roman', serif;
  }

  .orderviewBox .sumNum,
  .orderviewBox .statusNum {
    color: #b08a4d;
  }

  .homeMain {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 14px;
    .container { margin-bottom: 0; }
  }
}
</style>
