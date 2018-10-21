<template>

  
    
    <!-- <h1>{{ msg }}</h1> -->
    <div id="map"></div>
  </div>
</template>

<script>

import { map, invalidateSize, marker, Icon, LeafIcon , geoJSON} from "leaflet"

import { basemapLayer, featureLayer, tiledMapLayer, post, dynamicMapLayer } from 'esri-leaflet';

import 'leaflet/dist/leaflet.css';

export default {
  name: 'Map',
  props: {
    msg: String
  },
  mounted() {
      const ourMap = map('map', {
        minZoom: 0,
        maxZoom: 19
      }).setView([37.5407, -77.4360],8);
      basemapLayer('Gray').addTo(ourMap);
      ourMap.invalidateSize();

      const footPrints = dynamicMapLayer({
      url: 'https://gismaps.vita.virginia.gov/arcgis/rest/services/VA_Base_layers/VA_Building_Footprints/MapServer',
        useCors:false,
      }).addTo(ourMap);

      const solar = featureLayer({
        url: 'https://services1.arcgis.com/s9zcgAj2OICmYdJ5/ArcGIS/rest/services/NREL_10km_Lower_48_and_Hawaii_GHI_10km_Resolution_1998_to_2009/FeatureServer/0',
        useCors: false,
        style: function (feature) {
          if(feature.properties.ANN_GHI*1 < 3.9504954338){
            return {color: '#ff0000', weight: 0 };
          } else if(feature.properties.ANN_GHI*1 < 4.3786504566){
            return { color: '#ff9000', weight: 0 };
          } else if(feature.properties.ANN_GHI*1 < 4.8341397260000001) { 
            return { color: '#ff9000', weight: 0 };
          } else {
            return { color: '#fcdab5', weight: 0 };
          }
        }
      }).addTo(ourMap);
      //wind or solar
      ourMap.on('zoomend', ()=> {
          if (ourMap.getZoom() >12){
              if (ourMap.hasLayer(solar)) {
                  ourMap.removeLayer(solar);
              } else {
                  console.log("no point layer active");
              }
          }
          if (ourMap.getZoom() <= 12){
              if (ourMap.hasLayer(solar)){
                  console.log("layer already added");
              } else {
                  ourMap.addLayer(solar);
              }
          }
      });

      var selectedStyle = {
        "color": "#ff7800",
        "weight": 5,
        "opacity": 0.65
      };


      const selectFootPrtints = geoJSON( [],{style: selectedStyle}).addTo(ourMap);

      footPrints.bindPopup((error, featureCollection)=>{
        if(error || featureCollection.features.length === 0) {
          
        } else {
          
          selectFootPrtints.clearLayers()
          selectFootPrtints.addData(featureCollection);
          this.featureSelected(featureCollection);
        }
      });
  },

  methods:{
     featureSelected(feature){
       this.$emit('featureSelected', feature)
     }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
body {
            padding: 0;
            margin: 0;
        }

h3 {
  margin: 40px 0 0;
}
#map{
  width: 100%;
  height: 1000px;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
