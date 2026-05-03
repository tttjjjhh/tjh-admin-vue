<template>
  <InkFilterBar>
    <div>
      <InkTabs v-model="current" :options="tabOptions" @input="changeTab" />
      <p class="range">已选时间：{{ tateData[0] }} 至 {{ tateData[tateData.length - 1] }}</p>
    </div>
    <template #actions>
      <el-button icon="iconfont icon-download" class="right-el-button" @click="handleExport">数据导出</el-button>
    </template>
  </InkFilterBar>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from 'vue-property-decorator'
import { exportInfor } from '@/api/index'
import { InkFilterBar, InkTabs } from '@/components/ink'
@Component({
  name: 'TitleIndex',
  components: { InkFilterBar, InkTabs },
})
export default class extends Vue {
  @Prop() private flag!: any
  @Prop() private tateData!: any

  current = 2
  tabOptions = [
    { label: '昨日', value: 1 },
    { label: '近7日', value: 2 },
    { label: '近30日', value: 3 },
    { label: '本周', value: 4 },
    { label: '本月', value: 5 },
  ]

  @Watch('flag')
  getNowIndex(val) {
    this.current = val
  }

  changeTab(value: number) {
    this.current = value
    this.$emit('sendTitleInd', value)
  }

  handleExport() {
    this.$confirm('是否确认导出最近30天运营数据?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning',
    })
      .then(async function() {
        const { data } = await exportInfor()
        const url = window.URL.createObjectURL(data)
        const a = document.createElement('a')
        document.body.appendChild(a)
        a.href = url
        a.download = '运营数据统计报表.xlsx'
        a.click()
        window.URL.revokeObjectURL(url)
      })
      .then(() => {})
  }
}
</script>

<style scoped>
.range{margin-top:10px;color:#6d7784;font-size:13px}
</style>
