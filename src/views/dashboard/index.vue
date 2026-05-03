<template>
  <InkPage class="dashboard-home">
    <InkCard>
      <InkSectionTitle title="今日数据" :date="days[1]">
        <template #actions>
          <router-link to="/statistics" class="detail-link">详细数据</router-link>
        </template>
      </InkSectionTitle>
      <div class="stats-grid">
        <InkStatCard title="营业额" :value="`¥ ${overviewData.turnover || 0}`">
          <template #icon><i class="iconfont icon-jine_m-2"></i></template>
        </InkStatCard>
        <InkStatCard title="有效订单" :value="overviewData.validOrderCount || 0" />
        <InkStatCard title="订单完成率" :value="`${completionRate}%`" />
        <InkStatCard title="平均客单价" :value="`¥ ${overviewData.unitPrice || 0}`" />
        <InkStatCard title="新增用户" :value="overviewData.newUsers || 0" />
      </div>
    </InkCard>

    <InkCard>
      <InkSectionTitle title="订单管理" :date="days[1]">
        <template #actions>
          <router-link to="/order" class="detail-link">订单明细</router-link>
        </template>
      </InkSectionTitle>
      <div class="order-grid">
        <router-link class="order-item" to="/order?status=2"><span><i class="iconfont icon-waiting"></i>待接单</span><b>{{ orderviewData.waitingOrders || 0 }}</b></router-link>
        <router-link class="order-item" to="/order?status=3"><span><i class="iconfont icon-staySway"></i>待派送</span><b>{{ orderviewData.deliveredOrders || 0 }}</b></router-link>
        <router-link class="order-item" to="/order?status=5"><span><i class="iconfont icon-complete"></i>已完成</span><b>{{ orderviewData.completedOrders || 0 }}</b></router-link>
        <router-link class="order-item" to="/order?status=6"><span><i class="iconfont icon-cancel"></i>已取消</span><b>{{ orderviewData.cancelledOrders || 0 }}</b></router-link>
        <router-link class="order-item" to="/order"><span><i class="iconfont icon-all"></i>全部订单</span><b>{{ orderviewData.allOrders || 0 }}</b></router-link>
      </div>
    </InkCard>

    <div class="double-grid">
      <InkCard>
        <InkSectionTitle title="菜品总览">
          <template #actions><router-link to="/dish" class="detail-link">菜品管理</router-link></template>
        </InkSectionTitle>
        <div class="summary-wrap">
          <div class="summary-item"><span>已启售</span><b>{{ dishesData.sold || 0 }}</b></div>
          <div class="summary-item"><span>已停售</span><b>{{ dishesData.discontinued || 0 }}</b></div>
          <router-link class="add-btn" to="/dish/add">新增菜品</router-link>
        </div>
      </InkCard>

      <InkCard>
        <InkSectionTitle title="套餐总览">
          <template #actions><router-link to="/setmeal" class="detail-link">套餐管理</router-link></template>
        </InkSectionTitle>
        <div class="summary-wrap">
          <div class="summary-item"><span>已启售</span><b>{{ setMealData.sold || 0 }}</b></div>
          <div class="summary-item"><span>已停售</span><b>{{ setMealData.discontinued || 0 }}</b></div>
          <router-link class="add-btn" to="/setmeal/add">新增套餐</router-link>
        </div>
      </InkCard>
    </div>

    <InkCard class="order-empty-card">
      <InkSectionTitle title="订单信息" />
      <InkEmptyState title="暂无订单信息" description="山水悠悠，静候佳音" />
    </InkCard>

    <OrderList
      :order-statics="orderStatics"
      @getOrderListBy3Status="getOrderListBy3Status"
    />
  </InkPage>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import {
  getBusinessData,
  getOrderData,
  getOverviewDishes,
  getSetMealStatistics,
} from '@/api/index'
import { getOrderListBy } from '@/api/order'
import { getday } from '@/utils/formValidate'
import OrderList from './components/orderList.vue'
import {
  InkPage,
  InkCard,
  InkSectionTitle,
  InkStatCard,
  InkEmptyState,
} from '@/components/ink'

@Component({
  name: 'Dashboard',
  components: {
    InkPage,
    InkCard,
    InkSectionTitle,
    InkStatCard,
    InkEmptyState,
    OrderList,
  },
})
export default class extends Vue {
  private overviewData = {}
  private orderviewData = {} as any
  private dishesData = {} as any
  private setMealData = {}
  private orderStatics = {} as any

  get days() {
    return getday()
  }

  get completionRate() {
    return Number(((this.overviewData as any).orderCompletionRate || 0) * 100).toFixed(0)
  }

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
  async getBusinessData() {
    const data = await getBusinessData()
    this.overviewData = data.data.data
  }
  async getOrderStatisticsData() {
    const data = await getOrderData()
    this.orderviewData = data.data.data
  }
  async getOverStatisticsData() {
    const data = await getOverviewDishes()
    this.dishesData = data.data.data
  }
  async getSetMealStatisticsData() {
    const data = await getSetMealStatistics()
    this.setMealData = data.data.data
  }
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

<style lang="scss" scoped>
.dashboard-home{position:relative}
.dashboard-home::before{content:'';position:absolute;inset:0;pointer-events:none;background:linear-gradient(180deg,rgba(123,134,150,.12),rgba(123,134,150,0) 180px)}
.detail-link{color:var(--ink-gold);font-size:14px}
.stats-grid{display:grid;grid-template-columns:repeat(5,minmax(0,1fr));gap:14px}
.order-grid{display:grid;grid-template-columns:repeat(5,minmax(0,1fr));gap:12px}
.order-item{display:flex;justify-content:space-between;align-items:center;padding:16px;border:1px solid #e8dfd1;border-radius:12px;background:linear-gradient(180deg,#fff,#fcf9f3);color:#2f3a45}
.order-item b{font-size:38px;color:#b08a4d;font-family:'Times New Roman',serif;line-height:1}
.order-item span i{margin-right:6px;color:#4a6174}
.double-grid{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.summary-wrap{display:grid;grid-template-columns:1fr 1fr auto;gap:12px;align-items:stretch}
.summary-item{border:1px solid #e8dfd1;border-radius:10px;padding:18px;background:#fff}
.summary-item span{display:block;color:var(--ink-text-secondary)}
.summary-item b{display:block;font-size:42px;color:#1c2530;font-family:'Times New Roman',serif;margin-top:8px}
.add-btn{display:flex;align-items:center;justify-content:center;padding:0 20px;border-radius:12px;border:1px solid #c8a96d;background:linear-gradient(90deg,#2f4858,#3c5d71);color:#fff;min-width:130px}
.order-empty-card{margin-top:14px}
</style>
