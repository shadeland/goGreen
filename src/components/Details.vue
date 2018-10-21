<template>

  <div class="hello">
    <div class="row" style="margin-top:25px">
      <div class="card">
        <div class="card-header">
          Go Green Estimation
        </div>
        <div class="card-body">
              <div class="form-group text-left" >
                <label>Area (sqft):</label>
                <b-form-input v-model="area"
                              type="text"
                              placeholder="Enter Area"></b-form-input>
              </div>
              <div class="form-group text-left">
                
                <label>Solar Surface:</label> 
                  <range-slider
                      class="slider"
                      min="0"
                      max="100"
                      step="5"
                      v-model="areaPerc" /> <h6>
                        {{areaPerc}}%
                      </h6>
              </div>
                <div class="form-group text-left">
                  <label>Building Type:</label> 
                <b-form-select v-model="type" :options="typeOptions" class="mb-3" />
                <label>Wind (kw):</label>
                <b-form-input v-model="wind"
                              type="text"
                              placeholder="Enter wind"></b-form-input>
                <b-form-select v-model="energy" :options="energyOptions" class="mb-3" />
                 
                <b-button>Calculate</b-button> 
                </div>
            <table class="table table-striped">
              
              <tbody>
                <tr>
                  
                  <td>Solar PV Capacity</td>
                  <td>{{pvPot.pv.toFixed(2)}}</td>
                  <td>kw</td>
                </tr>
                <tr>
                  
                  <td>Cost Total</td>
                  <td>{{pvPot.ctotal.toFixed(2)}}</td>
                  <td>$</td>
                </tr>
                 <tr>
                  
                  <td>Earning/Year</td>
                  <td>{{pvPot.earnings_per_year.toFixed(2)}}</td>
                  <td>kwh/y</td>
                </tr>
                 <tr>
                  
                  <td>Saving/Year</td>
                  <td>{{pvPot.savings_per_year.toFixed(2)}}</td>
                  <td>$</td>
                </tr>
                 <tr>
                  
                  <td>Emission Reduction CO2</td>
                  <td>{{pvPot.em_co2.toFixed(2)}}</td>
                  <td>lbs</td>
                </tr>
                 <tr>
                  
                  <td>Emission Reduction CH4</td>
                  <td>{{pvPot.em_ch4.toFixed(2)}}</td>
                  <td>lbs</td>
                </tr>
                 <tr>
                  
                  <td>Emission Reduction N2O</td>
                  <td>{{pvPot.em_n2o.toFixed(2)}}</td>
                  <td>lbs</td>
                </tr>
              </tbody>
            </table>   
           <b-button>Fund me</b-button> 
        </div>
      </div>
    </div>
    <div class="row" style="margin-top:25px">
      
        <div class="card">
          <div class="card-header">
            Smart Scheduling 
          </div>
          <div class="card-body">
              <div class="form-group text-left" >
                <label>Solar PV Capacity (kw):</label>
                <b-form-input v-model="smartPv"
                              type="text"
                              placeholder="Enter PV"></b-form-input>
              </div>
              <div class="form-group text-left" >
                <label>Participation Willingness(%)</label>
                <b-form-input v-model="smartWillingness"
                              type="text"
                              placeholder="Enter Value"></b-form-input>
              </div>
              <div class="form-group text-left" >
                <label>Base Load Profile:</label> </br>
                <input type="file"  id="inputGroupFile01"/>
                
              </div>
               <div class="form-group text-left" >
                
                 <b-btn v-b-modal.modal1>Launch</b-btn>

  <!-- Modal Component -->
                <b-modal id="modal1" class="modal-lg" title="Smart Schedule Report" @show='modalShow'>
                  <p id="modalInside" class="my-4">
                    
                  </p>
                </b-modal>
              </div>
              
          </div>
        
      </div>
    </div>
  </div>
</template>

<script>

// import { map, invalidateSize, marker, Icon, LeafIcon } from "leaflet"

// import { basemapLayer, featureLayer, tiledMapLayer, post, dynamicMapLayer } from 'esri-leaflet';

// import 'leaflet/dist/leaflet.css';
import RangeSlider from 'vue-range-slider'
// you probably need to import built-in style
import 'vue-range-slider/dist/vue-range-slider.css'

export default {
  name: 'Details',
  data(){
    return {
      wind:0, 
      area:0,
      type:null,
      areaPerc:1,
      typeOptions:[
        { value: 'residential', text: 'Residential' },
        { value: 'commercial', text: 'Commercial' },
      ],
      energyOptions:[
        { value: 'solar', text: 'Solar' },
        { value: 'wind', text: 'Wind' },
      ],
      energy:'solar',
      pvPot:{}, 
      smartPv:0,
      smartWillingness:0

    }
  },
  components:{
     RangeSlider,
  },
  watch: { 
    feature: function(){
      
      this.area = this.feature['features'][0]['properties']['Shape.area'];
       let area = this.feature['features'][0]['properties']['Shape.area'] * 0.092903 * this.areaPerc/100;
      this.pvPot = this.calculate_pv_potential(area, this.type)
      console.log(this.type);
      
    },
    type: function(){
      
      this.area = this.feature['features'][0]['properties']['Shape.area'];
       let area = this.feature['features'][0]['properties']['Shape.area'] * 0.092903 * this.areaPerc/100;
      this.pvPot = this.calculate_pv_potential(area, this.type)
      console.log(this.type);
    },
    areaPerc: function(){
      
      this.area = this.feature['features'][0]['properties']['Shape.area'];
       let area = this.feature['features'][0]['properties']['Shape.area'] * 0.092903 * this.areaPerc/100;
      this.pvPot = this.calculate_pv_potential(area, this.type)
      console.log(this.type);
    },
    energy: function(){
      if(this.energy==='solar')
        {this.area = this.feature['features'][0]['properties']['Shape.area'];
        let area = this.feature['features'][0]['properties']['Shape.area'] * 0.092903 * this.areaPerc/100;
        this.pvPot = this.calculate_pv_potential(area, this.type)}
      else{
         this.pvPot = this.calclulate_wind_potential(this.wind)
         console.log(this.pvPot);
         
      }
      
     
    }

  },
  props: {
    feature: Object
  },
  created(){
    console.log("hey");
    
  },
  mounted() {
     
  }, 

  methods:{
      modalShow(e){
        
        // console.log(e.target.getElementsByClassName("my-4").innerHtml);
        document.getElementById('modalInside').innerHTML = "Calculating...";
        
        setTimeout(()=>{
          let img = document.createElement("img");
          img.src = 
            document.getElementById('modalInside').innerHTML = 
          `
              <img style="width:420px; height=315px" src="/load_pv${this.smartPv}_w${this.smartWillingness}.jpg" />
              <img style="width:420px; height=315px" src="/mix_pv${this.smartPv}_w${this.smartWillingness}.jpg" />
             
            `
        },2000);
        
        
        
        
      },
      calculate_pv_potential(area,type) {
          var pv;
          var savings_per_year;
          var earnings_per_year;
          var em_co2;
          var em_ch4;
          var em_n2o;

          var std_pv = 0.16;

          pv = area * std_pv ;
          var ctotal = 3.05 * pv * 1000;

          earnings_per_year = 1000 * pv * 0.86;
          if(type=='residential')
            savings_per_year = earnings_per_year * (0.11 + 0.012);
          else
            savings_per_year = earnings_per_year * (0.08 + 0.012);
          var em_co2 =  earnings_per_year * 0.863;
          var em_ch4 = earnings_per_year *0.001 * 0.099;
          var em_n20 = earnings_per_year *0.001* 0.014;

              return {"area":area,
                      "pv":pv,
                      "ctotal":ctotal,
                      "earnings_per_year":earnings_per_year,
                      "savings_per_year":savings_per_year,
                      "em_co2":em_co2,
                      "em_ch4":em_ch4,
                      "em_n2o":em_n20
                    }


      },

      calclulate_wind_potential(wind) {
        var pv;
        var savings_per_year;
        var earnings_per_year;
        var em_co2;
        var em_ch4;
        var em_n2o;
        var ctotal;


        ctotal = 795 * wind ;

        earnings_per_year = wind * 8760 * 0.39 ;
        savings_per_year = earnings_per_year * (0.14 + 0.012);

        em_co2 = earnings_per_year * 0.863;
        em_ch4 = earnings_per_year * 0.001 * 0.099;
        em_n2o = earnings_per_year * 0.001 * 0.014;


        return{ "wind":wind,
                "pv": 0,
                "ctotal":ctotal,
                "earnings_per_year":earnings_per_year,
                "savings_per_year":savings_per_year,
                "em_co2":em_co2,
                "em_ch4":em_ch4,
                "em_n2o":em_n2o
              }

      }
  }, 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">

h3 {
  margin: 40px 0 0;
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
