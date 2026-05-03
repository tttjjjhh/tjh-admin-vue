<template>
  <div class="ink-empty">
    <div class="illustration" />
    <h3>{{ title }}</h3>
    <p>{{ description }}</p>
    <slot name="actions" />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
@Component({ name: 'InkEmptyState' })
export default class InkEmptyState extends Vue {
  @Prop({ default: '暂无数据' }) private title!: string
  @Prop({ default: '山水悠悠，静候佳音' }) private description!: string
}
</script>

<style lang="scss" scoped>
.ink-empty{text-align:center;padding:42px 16px;color:var(--ink-text-secondary)}
.illustration{height:130px;margin:0 auto 10px;max-width:580px;background:radial-gradient(circle at 20% 65%, rgba(47,72,88,.16), transparent 40%),radial-gradient(circle at 70% 70%, rgba(176,138,77,.18), transparent 45%)}
h3{margin:6px 0;color:var(--ink-text-strong);font-family:'STKaiti','KaiTi',serif;font-size:28px}
</style>
