<template>
	
	<div class="container">
	<div class="gameover" v-if="gameOver"><h1>Game over</h1></div>
	<div class="info_bar">

	<div class="countryname_flag">
	<h4>Find this country <span>{{randomCountry.countryName}}</span> </h4>
	<img :src="`https://www.countryflags.io/${randomCountry.countryId}/shiny/32.png`">
	</div>
	
	<div class="score_life">
	<h4 class="score">Score : {{score}}</h4>
	<h4>Lifes : {{lifes}}</h4>
	</div>
	</div>
	<!-- <button @click="startGame">Start</button> -->
	<div id="chartdiv" ></div>
	</div>
	
  
</template>

<script lang="ts">
import { defineComponent, ref, Ref} from "vue";
import * as am4core from "@amcharts/amcharts4/core";
import * as am4maps from "@amcharts/amcharts4/maps";
import am4geodata_worldLow from "@amcharts/amcharts4-geodata/worldLow";
// import { any } from "@amcharts/amcharts4/.internal/core/utils/Array";

interface country {
	countryId : string,
	countryName : string
}


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

	//testing

	//testing --end
	// mapTemplate.fill = am4core.color("#98EA8A");

	let hs = mapTemplate.states.create("hover");
	hs.properties.fill = am4core.color("#6ED35D");

	let countryList : Ref<any[]> = ref([])
	let countryNamdAndId : Ref<any[]> = ref([])
	let randomCountry : Ref<country>= ref({
		countryId : '',
		countryName : ''
	})
	let score : Ref<number> = ref(0)
	let lifes : Ref<number> = ref(3)
	let gameOver : Ref<boolean> = ref(false)
	
	

	countryList.value  = WorldMap.data
	// console.log(countryList)

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
		getRandomCountry()
	}, 0)
	
	function myFunction(ev : any) {
		
		// console.log(ev.target.dataItem.dataContext.name);
		if(randomCountry.value.countryName === ev.target.dataItem.dataContext.name) {
			score.value +=1;
			ev.target.fill =am4core.color("#F3CC3B")
			getRandomCountry()
			return score
		} else {
			
			lifes.value -= 1
			ev.target.fill =am4core.color("#FF0000")
		}
		if(lifes.value === 0) {
			let answer = WorldMap.getPolygonById(randomCountry.value.countryId);
			answer.isHover = true;
			mapTemplate.tooltipText = "{randomCountry.value.countryName}";
			gameOver.value = true
		}
		// console.log('You are wrong')

	}
	
	mapTemplate.events.on("hit", myFunction);
	
	//map.series.template.events.off("hit", myFunction, this);
		
	
	
	
	return {
		randomCountry,
		countryList,
		countryNamdAndId,
		score,
		lifes,
		gameOver
	}
}    
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.container{
	overflow: hidden;
}
h1 {
  color: blue;
}

.info_bar, .countryname_flag, .score_life {
	display: flex;
	justify-content: space-around;
	align-items: center;
}

span, .score {
	margin-right: 1rem;
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

/* svg path {
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
} */

span {
	color: brown;
}

</style>
