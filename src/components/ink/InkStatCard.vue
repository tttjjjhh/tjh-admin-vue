<template>
  <InkCard class="ink-stat-card" size="md">
    <div class="head">{{ title }}</div>
    <div class="value">{{ value }}</div>
    <div class="foot"><slot name="icon" /></div>
  </InkCard>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import InkCard from './InkCard.vue'
@Component({ name: 'InkStatCard', components: { InkCard } })
export default class InkStatCard extends Vue {
  @Prop({ required: true }) private title!: string
  @Prop({ required: true }) private value!: string | number
}
</script>

<style lang="scss" scoped>
.ink-stat-card{position:relative;overflow:hidden;min-height:120px}
.ink-stat-card::after{content:'';position:absolute;right:-12px;bottom:-14px;width:120px;height:80px;background:radial-gradient(circle,rgba(47,72,88,.12),rgba(47,72,88,0) 70%)}
.head{color:var(--ink-text-secondary);font-size:14px}
.value{font-size:42px;line-height:1.2;color:var(--ink-text-strong);font-family:'Times New Roman',serif}
.foot{position:absolute;right:14px;top:14px;color:var(--ink-gold)}
</style>
