<template>
  <section class="carousel-section"
           ref="itemRefs"
  >
    <div class="carousel-item">
      <slot></slot>
    </div>
  </section>
</template>

<script lang="ts">
import {defineComponent} from 'vue'

export default defineComponent({
  name: "SectionItem"
})
</script>

<style scoped>

</style>