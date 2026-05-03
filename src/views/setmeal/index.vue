<template>
  <InkPage class="setmeal-page">
    <InkCard>
      <InkFilterBar class="tableBar">
        <div class="filter-item">
          <label>套餐名称：</label>
          <el-input
            v-model="input"
            placeholder="请填写套餐名称"
            style="width: 220px"
            clearable
            @clear="init"
            @keyup.enter.native="initFun"
          />
        </div>
        <div class="filter-item">
          <label>套餐分类：</label>
          <el-select
            v-model="categoryId"
            style="width: 200px"
            placeholder="请选择"
            clearable
            @clear="init"
          >
            <el-option v-for="item in dishCategoryList" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </div>
        <div class="filter-item">
          <label>售卖状态：</label>
          <el-select
            v-model="dishStatus"
            style="width: 160px"
            placeholder="请选择"
            clearable
            @clear="init"
          >
            <el-option v-for="item in saleStatus" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </div>
        <template #actions>
          <el-button class="normal-btn continue" @click="init(true)">查询</el-button>
          <el-button class="ink-danger-ghost" @click="deleteHandle('批量', null)">批量删除</el-button>
          <el-button type="primary" class="ink-primary-btn" @click="addSetMeal('add')">+ 新增套餐</el-button>
        </template>
      </InkFilterBar>
    </InkCard>

    <InkTableWrapper :class="{ hContainer: tableData.length }">
      <el-table
        v-if="tableData.length"
        :data="tableData"
        stripe
        class="tableBox"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="40" />
        <el-table-column prop="name" label="套餐名称" min-width="160" />
        <el-table-column prop="image" label="图片" width="120">
          <template slot-scope="{ row }">
            <el-image class="cover-thumb" :src="row.image">
              <div slot="error" class="image-slot">
                <img src="./../../assets/noImg.png" class="cover-fallback">
              </div>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="categoryName" label="套餐分类" min-width="120" />
        <el-table-column prop="price" label="套餐价" min-width="120">
          <template slot-scope="scope">
            <span>￥{{ ((scope.row.price).toFixed(2) * 100) / 100 }}</span>
          </template>
        </el-table-column>
        <el-table-column label="售卖状态" min-width="120">
          <template slot-scope="scope">
            <div class="status-cell" :class="{ 'is-stop': String(scope.row.status) === '0' }">
              <span class="status-dot" />
              <span>{{ String(scope.row.status) === '0' ? '停售' : '启售' }}</span>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="updateTime" label="最后操作时间" min-width="180" />
        <el-table-column label="操作" width="250" align="center">
          <template slot-scope="scope">
            <el-button type="text" size="small" class="ink-action-btn" @click="addSetMeal(scope.row)">修改</el-button>
            <el-button type="text" size="small" class="ink-muted-danger" @click="deleteHandle('单删', scope.row.id)">删除</el-button>
            <el-button type="text" size="small" class="ink-action-btn" @click="statusHandle(scope.row)">
              {{ scope.row.status == '0' ? '启售' : '停售' }}
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <Empty v-else :is-search="isSearch" />

      <template #pagination>
        <el-pagination
          v-if="counts > 10"
          class="pageList"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="counts"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
        />
      </template>
    </InkTableWrapper>
  </InkPage>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import {
  getSetmealPage,
  deleteSetmeal,
  setmealStatusByStatus,
  dishCategoryList
} from '@/api/setMeal'
import Empty from '@/components/Empty/index.vue'

@Component({
  name: 'package',
  components: {
    Empty
  }
})
export default class extends Vue {
  private input: any = ''
  private counts: number = 0
  private page: number = 1
  private pageSize: number = 10
  private checkList: any[] = []
  private tableData: any[] = []
  private dishCategoryList = []
  private categoryId = ''
  private dishStatus = ''
  private isSearch: boolean = false
  private saleStatus: any = [
    { value: 0, label: '停售' },
    { value: 1, label: '启售' }
  ]

  created() {
    this.init()
    this.getDishCategoryList()
  }

  initFun() {
    this.page = 1
    this.init()
  }

  private async init(isSearch?) {
    this.isSearch = isSearch
    await getSetmealPage({
      page: this.page,
      pageSize: this.pageSize,
      name: this.input || undefined,
      categoryId: this.categoryId || undefined,
      status: this.dishStatus
    })
      .then(res => {
        if (res && res.data && res.data.code === 1) {
          this.tableData = res.data.data.records
          this.counts = Number(res.data.data.total)
        } else {
          this.$message.error(res.data.msg)
        }
      })
      .catch(err => {
        this.$message.error('请求出错了：' + err.message)
      })
  }

  private addSetMeal(st: any) {
    if (st === 'add') this.$router.push({ path: '/setmeal/add' })
    else this.$router.push({ path: '/setmeal/add', query: { id: st.id } })
  }

  private deleteHandle(type: string, id: any) {
    if (type === '批量' && id === null && this.checkList.length === 0) {
      return this.$message.error('请选择删除对象')
    }
    this.$confirm('确定删除该套餐?', '确定删除', {
      confirmButtonText: '删除',
      cancelButtonText: '取消',
      type: 'warning'
    }).then(() => {
      deleteSetmeal(type === '批量' ? this.checkList.join(',') : id)
        .then(res => {
          if (res.data.code === 1) {
            this.$message.success('删除成功！')
            this.init()
          } else this.$message.error(res.data.msg)
        })
        .catch(err => {
          this.$message.error('请求出错了：' + err.message)
        })
    })
  }

  private statusHandle(row: any) {
    const params: any = {
      ids: row.id,
      status: row.status ? '0' : '1'
    }

    this.$confirm('确认更改该套餐状态?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    }).then(() => {
      setmealStatusByStatus(params)
        .then(res => {
          if (res.data.code === 1) {
            this.$message.success('套餐状态已经更改成功！')
            this.init()
          } else this.$message.error(res.data.msg)
        })
        .catch(err => {
          this.$message.error('请求出错了：' + err.message)
        })
    })
  }

  private getDishCategoryList() {
    dishCategoryList({ type: 2 })
      .then(res => {
        if (res && res.data && res.data.code === 1) {
          this.dishCategoryList = (res.data && res.data.data && res.data.data).map(item => ({ value: item.id, label: item.name }))
        }
      })
      .catch(() => {})
  }

  private handleSelectionChange(val: any) {
    this.checkList = val.map((n: any) => n.id)
  }

  private handleSizeChange(val: any) {
    this.pageSize = val
    this.init()
  }

  private handleCurrentChange(val: any) {
    this.page = val
    this.init()
  }
}
</script>

<style lang="scss" scoped>
.setmeal-page {
  .tableBar {
    ::v-deep .ink-filter-actions {
      margin-left: auto;
      padding-left: 12px;
    }
  }

  ::v-deep .ink-filter-bar {
    padding: 6px 0;
  }

  ::v-deep .ink-filter-actions {
    gap: 12px;
  }

  @media (max-width: 1366px) {
    .tableBar {
      ::v-deep .ink-filter-actions {
        width: 100%;
        justify-content: flex-end;
      }
    }
  }

  .filter-item {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    margin-right: 18px;
    margin-bottom: 8px;
  }

  ::v-deep .el-table th > .cell,
  ::v-deep .el-table td > .cell {
    padding-top: 14px;
    padding-bottom: 14px;
  }

  ::v-deep .el-table .el-button--text {
    padding: 0 2px;
  }

  .ink-primary-btn {
    background: linear-gradient(135deg, #2f365a 0%, #3a446f 100%);
    border-color: #2f365a;
  }

  .ink-danger-ghost {
    color: #8b5e3c;
    border: 1px solid #d8c3ab;
    background: #fff;
  }

  .cover-thumb {
    width: 84px;
    height: 52px;
    border-radius: 8px;
    overflow: hidden;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    background: #f5f6f8;
  }

  ::v-deep .cover-thumb .el-image__inner {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .cover-fallback {
    width: 84px;
    height: 52px;
    object-fit: cover;
    border-radius: 8px;
    display: block;
  }

  .status-cell {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    color: #2f365a;

    .status-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: #52c41a;
    }

    &.is-stop {
      color: #8c8c8c;
      .status-dot {
        background: #bfbfbf;
      }
    }
  }

  .ink-action-btn {
    color: #2f365a;
  }

  .ink-muted-danger {
    color: #8b5e3c;
  }
}
</style>
