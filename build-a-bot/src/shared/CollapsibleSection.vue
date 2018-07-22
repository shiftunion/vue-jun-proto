<template>

<div>
  <div class="header">
        <span v-if="open" @click="open = !open">&#x25B2; Collpase</span>
        <span v-if="!open" @click="open = !open">&#x25BC; Expand</span>
    </div>
  <slot v-if="open">
      <div>Default content</div>
  </slot>
</div>

</template>

<script>
export default {
  name: 'CollapsibleSection',
  data() {
    return { open: true };
  },
  props: ['startOpen'],
  created() {
    this.open = this.startOpen;
  },
};
</script>

<style scoped>
.header {
  background-color: #bbb;
  padding: 3px;
  cursor: pointer;
}
</style>
