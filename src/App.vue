<template>
  <h1>{{message}}</h1>
</template>

<script>
  export default {
    name: "App",
    data() {
      return {
        message: "Hello Everyone!"
      }
    }
  }
</script>

<style>
  h1 {
    color: red;
  }
</style>