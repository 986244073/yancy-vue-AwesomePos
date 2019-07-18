<template>
  <div id="app">
    <!--    左侧导航-->
<!--    <leftNav></leftNav>-->
    <router-view/>
  </div>
</template>

<script>
  import leftNav from '@/components/common/leftNav'
  export default {
    name: 'App',
    components: {
      leftNav
    },
    data() {
      return {
      }
    },
  }
</script>

<style>
#app{
  font-family: 'Microsoft YaHei','Avenir','Helvetica','Arial','sans-serif';
  -webkit-font-smoothing: antialiased;

}
</style>
