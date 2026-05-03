<template>
  <article class="ink-card" :class="[`ink-card--${size}`]">
    <slot />
  </article>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
@Component({ name: 'InkCard' })
export default class InkCard extends Vue {
  @Prop({ default: 'md' }) private size!: 'sm' | 'md' | 'lg'
}
</script>

<style lang="scss" scoped>
.ink-card { background: var(--ink-card); border: 1px solid var(--ink-border); border-radius: var(--ink-radius-lg); box-shadow: var(--ink-shadow); }
.ink-card--sm{padding:12px}.ink-card--md{padding:18px}.ink-card--lg{padding:24px}
</style>
