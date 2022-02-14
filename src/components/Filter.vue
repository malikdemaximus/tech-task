<template>
  <div class="filter-box">
    <div class="filter-header flex">
      <h3>{{ title }}</h3>
      <div class="close" @click="resetFilter">
        <icon-close />
        <span class="tooltiptext">Сбросить выбор</span>
      </div>
    </div>
    <label class="checkbox-item" :key="key" v-for="(value, key) in options">
      {{ value }}
      <input type="checkbox" :value="key" v-model="checkedOptions" />
      <span class="checkmark"></span>
    </label>
  </div>
</template>

<script>
import IconClose from './IconClose.vue'
export default {
  name: 'Filter',
  props: {
    title: {
      type: String,
      reqired: true
    },
    options: {
      type: Object
    }
  },
  data() {
    return {
      checkedOptions: []
    }
  },
  watch: {
    checkedOptions(val) {
      this.$emit('update:modelValue', val)
    }
  },
  components: {
    IconClose
  },
  methods: {
    resetFilter() {
      this.checkedOptions = []
    }
  }
}
</script>

<style>
</style>