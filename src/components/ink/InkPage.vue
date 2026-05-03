<template>
  <section class="ink-page" :class="{ narrow }">
    <slot />
  </section>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component({ name: 'InkPage' })
export default class InkPage extends Vue {
  @Prop({ default: false }) private narrow!: boolean
}
</script>

<style lang="scss" scoped>
.ink-page { padding: 20px; max-width: 1800px; margin: 0 auto; }
.ink-page.narrow { max-width: 1280px; }
</style>
