<template>
  <ul>
    <li v-for="(el,i) in list" :key="i">{{list ? el : 'no selected item'}}</li>
  </ul>
</template>

<script>
export default {
  name: "SelectedList",
  props: {
    list: {
      type: Array
    }
  }
}
</script>

<style scoped>
  ul {
    margin-top: 400px;
    list-style: none;
  }

  li {
    font-size: 20px;
  }
</style>
