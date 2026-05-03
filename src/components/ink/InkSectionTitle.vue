<template>
  <header class="ink-section-title">
    <div class="left">
      <h2>{{ title }}</h2>
      <p v-if="subtitle">{{ subtitle }}</p>
      <span v-if="date" class="date">{{ date }}</span>
    </div>
    <div class="right"><slot name="actions" /></div>
  </header>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
@Component({ name: 'InkSectionTitle' })
export default class InkSectionTitle extends Vue {
  @Prop({ required: true }) private title!: string
  @Prop({ default: '' }) private subtitle!: string
  @Prop({ default: '' }) private date!: string
}
</script>

<style lang="scss" scoped>
.ink-section-title{display:flex;justify-content:space-between;align-items:flex-end;margin-bottom:14px;border-bottom:1px solid rgba(176,138,77,.22);padding-bottom:10px}
.left h2{margin:0;color:var(--ink-text-strong);font-family:'STKaiti','KaiTi',serif;font-size:28px}
.left h2::after{content:'  ~';color:var(--ink-gold-soft);font-weight:400}
.left p,.date{margin-left:8px;color:var(--ink-text-secondary);font-size:14px;display:inline}
.right{min-height:32px;display:flex;align-items:center}
</style>
