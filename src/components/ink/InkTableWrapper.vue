<template>
  <InkCard class="ink-table-wrapper" size="md">
    <div class="table-body"><slot /></div>
    <footer v-if="$slots.pagination" class="pagination"><slot name="pagination" /></footer>
  </InkCard>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import InkCard from './InkCard.vue'
@Component({ name: 'InkTableWrapper', components: { InkCard } })
export default class InkTableWrapper extends Vue {}
</script>

<style lang="scss" scoped>
.table-body{overflow:auto}
.pagination{display:flex;justify-content:flex-end;padding-top:14px;border-top:1px dashed var(--ink-border);margin-top:12px}
</style>
