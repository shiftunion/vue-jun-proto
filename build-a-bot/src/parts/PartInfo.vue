<template>
  <div>
    <h1>{{ part.title }}</h1>
    <div>
        {{ part.description }}
    </div>
  </div>
</template>

<script>

import getPartsMixin from './get-parts-mixin';

export default {
  name: 'PartInfo',
  mixins: [getPartsMixin],
  props: {
    partType: { type: String },
    id: {
      type: [Number, String],
      validator: v => Number.isInteger(Number(v)),
    },
  },
  computed: {
    part() {
      const { partType, id } = this; // destructuring from Props!
      return this.parts[partType].find(part => part.id === +id);
    },
  },
};
</script>
