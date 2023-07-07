<template>
  <div id="app">
    <Index />
    <!-- <Login /> -->
  </div>
</template>

<script>
import Login from "./views/Login.vue";
import Index from "./views/Index.vue";

export default {
  name: "App",
  components: {
    Login,
    Index,
  },
};
</script>

<style>
</style>
