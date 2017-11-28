<template>
  <div id="app">
     <leftnav></leftnav>
    <router-view/>
  </div>
</template>

<script>
import leftnav from './components/leftnav.vue';
export default {
  name: 'app',
  components:{
    leftnav
  }
}
</script>

<style>

#app{
  height: 100%;
}

</style>
