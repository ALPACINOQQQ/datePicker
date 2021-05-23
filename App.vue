<template>
  <div id="App">
    <DatePicker v-model="now"></DatePicker>
  </div>
</template>

<script>
import DatePicker from './date-picker'
export default {
  components: {
    DatePicker
  },
  data() {
    return {
      now: ''
    }
  },
  watch: {
    now(val) {
      console.log(val);
    }
  }
}
</script>

<style>
</style>