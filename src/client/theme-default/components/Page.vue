<template>
  <div class="page">
    <slot name="top" />

    <div class="content">
      <Content />
    </div>

    <PageEdit />
    <NextAndPrevLinks />

    <slot name="bottom" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import PageEdit from './PageEdit.vue'
import NextAndPrevLinks from './NextAndPrevLinks.vue'

export default defineComponent({
  components: {
    PageEdit,
    NextAndPrevLinks
  }
})
</script>

<style scoped>
.page {
  margin: 0 auto;
  padding: 0 1.5rem 1.5rem;
  max-width: 50rem;
}

.content {
  padding-bottom: 1.5rem;
}
</style>
