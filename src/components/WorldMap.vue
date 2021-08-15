<template>
	
	<div>
	<h4>Find this country <span>{{randomCountry.countryName}} </span> </h4>
	<!-- <button @click="startGame">Start</button> -->
	<div id="chartdiv"></div>
	</div>
	
  
</template>

<script lang="ts">
import { defineComponent, ref, Ref} from "vue";
import * as am4core from "@amcharts/amcharts4/core";
import * as am4maps from "@amcharts/amcharts4/maps";
import am4geodata_worldLow from "@amcharts/amcharts4-geodata/worldLow";
import { any } from "@amcharts/amcharts4/.internal/core/utils/Array";

interface country {
	countryId : string,
	countryName : string
}
// import Map from "./Map.vue";

export default defineComponent({
  name: "WorldMap",
  components:{},
  setup() {


	let map = am4core.create("chartdiv", am4maps.MapChart);
	map.geodata = am4geodata_worldLow;
	map.geodataSource.url = "/path/to/myCustomMap.json";
	map.projection = new am4maps.projections.Miller();

	let WorldMap = new am4maps.MapPolygonSeries();
	WorldMap.useGeodata = true;
	map.series.push(WorldMap);
	WorldMap.exclude = ["AQ"];

	let mapTemplate = WorldMap.mapPolygons.template;
	// mapTemplate.tooltipText = "{name}";
	// mapTemplate.tooltipText = "{id}";

	mapTemplate.fill = am4core.color("#cce1e9");

	let hs = mapTemplate.states.create("hover");
	hs.properties.fill = am4core.color("#2a6f97");




	let countryList : Ref<any[]> = ref([])
	let countryNamdAndId : Ref<any[]> = ref([])
	let randomCountry : Ref<country>= ref({
		countryId : '',
		countryName : ''
	})

	countryList.value  = WorldMap.data

	const getRandomCountry = () => {
		let randomNumber = Math.floor(Math.random() * countryNamdAndId.value.length)
		let country = countryNamdAndId.value[randomNumber]
		randomCountry.value = {
			countryId : country.countryId,
			countryName : country.countryName
		}
	}

	setTimeout(() => {
		for (let item of countryList.value) {
			let countryInfo = {
				countryName: item.name,
				countryId: item.id
			}
			
			countryNamdAndId.value.push(countryInfo)
		}	
		console.log('i am running')
		getRandomCountry()
	}, 0)
	
	

	
	
	// setTimeout(() => {

	// 	for (let item of countryList.value) {
	// 		let countryInfo = {
	// 			countryName: item.name,
	// 			countryId: item.id
	// 		}
			
	// 		countryNamdAndId.value.push(countryInfo)
	// 	}		
		
	// 	getRandomCountry()

	// })
	
	
	return {
		randomCountry,
		countryList,
		countryNamdAndId,
	}
}    
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  color: white;
}
.map_area {
  width: 80%;
  height: 90vh;
  margin: auto;
  background-color: #001524;
}

#chartdiv {
  width: 100%;
  height: 500px;
}

svg path {
  fill: #264653;
  stroke: whitesmoke;
  stroke-width: 0.25;
}

svg path:hover {
  fill: #2a6f97;
  cursor: pointer;
  transition: 0.5s;
}

svg {
  width: 100%;
  height: 100%;
}

span {
	color: brown;
}
/* svg {
   width="1009.6727"
   height="665.96301"
} */
</style>
