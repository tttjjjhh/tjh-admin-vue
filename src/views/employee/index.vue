<template>
  <InkPage class="employee-page">
    <InkCard>
      <InkFilterBar class="tableBar">
        <div class="filter-item">
          <label>员工姓名：</label>
          <el-input
            v-model="input"
            placeholder="请输入员工姓名"
            style="width: 220px"
            clearable
            @clear="init"
            @keyup.enter.native="initFun"
          />
        </div>
        <template #actions>
          <el-button class="normal-btn continue" @click="init(true)">查询</el-button>
          <el-button type="primary" class="ink-primary-btn" @click="addEmployeeHandle('add', '')">+ 添加员工</el-button>
        </template>
      </InkFilterBar>
    </InkCard>

    <InkTableWrapper :class="{ hContainer: tableData.length }">
      <el-table v-if="tableData.length" :data="tableData" stripe class="tableBox">
        <el-table-column prop="name" label="员工姓名" min-width="140" />
        <el-table-column prop="username" label="账号" min-width="120" />
        <el-table-column prop="phone" label="手机号" min-width="140" />
        <el-table-column label="账号状态" min-width="120">
          <template slot-scope="scope">
            <div class="status-cell" :class="{ 'is-stop': String(scope.row.status) === '0' }">
              <span class="status-dot" />
              <span>{{ String(scope.row.status) === '0' ? '禁用' : '启用' }}</span>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="updateTime" label="最后操作时间" min-width="180" />
        <el-table-column label="操作" width="180" align="center">
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              class="ink-action-btn"
              :class="{ 'disabled-text': scope.row.username === 'admin' }"
              :disabled="scope.row.username === 'admin'"
              @click="addEmployeeHandle(scope.row.id, scope.row.username)"
            >
              修改
            </el-button>
            <el-button
              :disabled="scope.row.username === 'admin'"
              type="text"
              size="small"
              class="ink-action-btn"
              :class="{ 'disabled-text': scope.row.username === 'admin' }"
              @click="statusHandle(scope.row)"
            >
              {{ scope.row.status == '1' ? '禁用' : '启用' }}
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <Empty v-else :is-search="isSearch" />

      <template #pagination>
        <el-pagination
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
import { getEmployeeList, enableOrDisableEmployee } from '@/api/employee'
import Empty from '@/components/Empty/index.vue'

@Component({
  name: 'Employee',
  components: {
    Empty
  }
})
export default class extends Vue {
  private input: any = ''
  private counts: number = 0
  private page: number = 1
  private pageSize: number = 10
  private tableData = []
  private id = ''
  private status = ''
  private isSearch: boolean = false

  created() {
    this.init()
  }

  initFun() {
    this.page = 1
    this.init()
  }

  private async init(isSearch?: boolean) {
    this.isSearch = isSearch
    const params = {
      page: this.page,
      pageSize: this.pageSize,
      name: this.input ? this.input : undefined
    }
    await getEmployeeList(params)
      .then((res: any) => {
        if (String(res.data.code) === '1') {
          this.tableData = res.data && res.data.data && res.data.data.records
          this.counts = res.data.data.total
        }
      })
      .catch((err) => {
        this.$message.error('请求出错了：' + err.message)
      })
  }

  private addEmployeeHandle(st: string, username: string) {
    if (st === 'add') {
      this.$router.push({ path: '/employee/add' })
    } else {
      if (username === 'admin') return
      this.$router.push({ path: '/employee/add', query: { id: st } })
    }
  }

  private statusHandle(row: any) {
    if (row.username === 'admin') return
    this.id = row.id
    this.status = row.status
    this.$confirm('确认调整该账号的状态?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    }).then(() => {
      enableOrDisableEmployee({ id: this.id, status: !this.status ? 1 : 0 })
        .then((res) => {
          if (String(res.status) === '200') {
            this.$message.success('账号状态更改成功！')
            this.init()
          }
        })
        .catch((err) => {
          this.$message.error('请求出错了：' + err.message)
        })
    })
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
.employee-page {
  ::v-deep .ink-filter-bar {
    padding: 6px 0;
  }

  ::v-deep .ink-filter-actions {
    gap: 12px;
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

  .ink-primary-btn {
    background: linear-gradient(135deg, #2f365a 0%, #3a446f 100%);
    border-color: #2f365a;
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
}

.disabled-text {
  color: #bac0cd !important;
}
</style>
