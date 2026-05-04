<template>
  <div class="container ink-panel ink-panel-setmeal">
    <h2 class="homeTitle ink-panel-title">
      套餐总览<span><router-link to="setmeal">套餐管理</router-link></span>
    </h2>
    <div class="orderviewBox ink-data-list">
      <ul>
        <li>
          <span class="status"><i class="iconfont icon-open"></i>已启售</span>
          <span class="num">{{ setMealData.sold }}</span>
        </li>
        <li>
          <span class="status"><i class="iconfont icon-stop"></i>已停售</span>
          <span class="num">{{ setMealData.discontinued }}</span>
        </li>
        <li class="add ink-action-entry">
          <router-link to="setmeal/add">
            <i></i>
            <p>新增套餐</p>
          </router-link>
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator'
@Component({
  name: 'SetMeal',
})
export default class extends Vue {
  @Prop() private setMealData!: any
}
</script>
