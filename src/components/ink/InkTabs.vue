<template>
  <div class="ink-tabs">
    <button v-for="item in options" :key="item.value" class="tab" :class="{ active: item.value===value }" @click="$emit('input', item.value)">{{ item.label }}</button>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
@Component({ name: 'InkTabs' })
export default class InkTabs extends Vue {
  @Prop({ default: () => [] }) private options!: Array<{ label: string; value: string | number }>
  @Prop({ default: '' }) private value!: string | number
}
</script>

<style lang="scss" scoped>
.ink-tabs{display:inline-flex;padding:4px;background:#f4eee3;border:1px solid var(--ink-border);border-radius:999px;gap:4px}
.tab{border:0;background:transparent;padding:8px 16px;border-radius:999px;color:var(--ink-text-secondary);cursor:pointer}
.tab.active{background:var(--ink-primary);color:#fff;box-shadow:inset 0 -2px 0 rgba(0,0,0,.1)}
</style>
