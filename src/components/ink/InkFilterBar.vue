<template>
  <InkCard class="ink-filter-bar" size="sm">
    <div class="filters"><slot /></div>
    <div class="actions"><slot name="actions" /></div>
  </InkCard>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import InkCard from './InkCard.vue'
@Component({ name: 'InkFilterBar', components: { InkCard } })
export default class InkFilterBar extends Vue {}
</script>

<style lang="scss" scoped>
.ink-filter-bar{display:flex;justify-content:space-between;gap:16px;align-items:flex-start}
.filters{flex:1;display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:12px}
.actions{display:flex;gap:10px;align-items:center}
</style>
