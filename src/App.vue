<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-10">
           <Map msg="Welcome to Your Vue.js App" @featureSelected="featureSelected"/>
        </div>
        <div class="col">
           
            <Details  :feature="sFeature"/>  
          
        </div>
      </div>
    </div>
   
  </div>
</template>

<script>
import Map from './components/Map.vue'
import Details from './components/Details.vue'



export default {
  name: 'app',
  data(){
    return {
    sFeature:{}
    }
  },
  components: {
    Map,
    Details
  },
  created:{
    
  },
  methods: {
    featureSelected(feature) {
      console.log(feature)
      this.sFeature = feature;
    }, 
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
 
}
</style>
