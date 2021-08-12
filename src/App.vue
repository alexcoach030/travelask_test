<template>
  <div id="app">
    <Hotels/>
  </div>
</template>

<script>
import Hotels from "@/components/Hotels";


export default {
  name: 'App',
  components: {
    Hotels
  }
}
</script>

<style>
#app {
  font-family: KievitPro, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
@font-face {
  font-family: 'KievitPro';
  src: local('KievitPro'), local('KievitPro'),
  url("~@/assets/fonts/KievitPro/KievitPro-Bold.woff") format("woff"),
  url("~@/assets/fonts/KievitPro/KievitPro-ExtraLight.woff") format("woff"),
  url("~@/assets/fonts/KievitPro/KievitPro-Light.woff") format("woff"),
  url("~@/assets/fonts/KievitPro/KievitPro-Medium.woff") format("woff"),
  url("~@/assets/fonts/KievitPro/KievitPro-Regular.woff") format("woff"),
  url("~@/assets/fonts/KievitPro/KievitPro-Thin.woff") format("woff");
  font-weight: 100;
  font-style: normal;
}
</style>
