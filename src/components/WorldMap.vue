<template>
	
	<div class="container">
	<div class="gameover" v-if="gameOver"><h1>Game over</h1> <button class="restart" @click="restart">Restart</button></div>
	<div class="info_bar">

	<div class="countryname_flag">
	<h4>Find this country <span>{{randomCountry.countryName}}</span> </h4>
	<img :src="`https://www.countryflags.io/${randomCountry.countryId}/shiny/32.png`" >
	</div>
	
	<div class="score_life">
	<h4 class="score">Score : {{score}}</h4>
	<h4>Lifes : {{lifes}}</h4>
	</div>
	</div>
	<!-- <button @click="startGame">Start</button> -->
	<div id="chartdiv" >
		<button class="zoomout" @click="zoomOut">Rezoom</button>
	</div>
	</div>
	
  
</template>

<script lang="ts">
import { defineComponent, ref, Ref} from "vue";
import * as am4core from "@amcharts/amcharts4/core";
import * as am4maps from "@amcharts/amcharts4/maps";
import am4geodata_worldLow from "@amcharts/amcharts4-geodata/worldLow";
import { any } from "@amcharts/amcharts4/.internal/core/utils/Array";
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


	var WorldMap = map.series.push(new am4maps.MapPolygonSeries());
	WorldMap.exclude = ["AQ","UM-FQ","TV"];
	WorldMap.useGeodata = true;
	WorldMap.calculateVisualCenter = true;
	

	let worldMapTemplate = WorldMap.mapPolygons.template;
	// worldMapTemplate.tooltipText = "{name}";
	worldMapTemplate.fill = am4core.color("#98EA8A");
	
	let colorSet = new am4core.ColorSet()
	

	let hoverState = worldMapTemplate.states.create("hover");
	hoverState.properties.fill = am4core.color("#FE82B6");
	worldMapTemplate.tooltipPosition = "fixed";
	

	let countryList : Ref<any[]> = ref([])
	countryList.value  = WorldMap.data
	let countryNamdAndId : Ref<any[]> = ref([])
	let randomCountry : Ref<country>= ref({
		countryId : '',
		countryName : ''
	})
	let score : Ref<number> = ref(0)
	let lifes : Ref<number> = ref(5)
	let gameOver : Ref<boolean> = ref(false)
	
	
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


	worldMapTemplate.events.on("hit", makeGuess);

	const checkIfCorrectAnswer = (country : any) => {
		if(randomCountry.value.countryName === country.target.dataItem.dataContext.name) {
			
			country.target.fill = am4core.color("#F3CC3B")
			getRandomCountry()
			return score.value +=1;
		} 
		lifes.value -= 1
		country.target.fill = am4core.color("#FF0000")
	}

	const showCorrectAnswer = (answer : any) => {
		if(lifes.value === 2) {
			answer.value = WorldMap.getPolygonById(randomCountry.value.countryId);
			answer.value.series.chart.zoomToMapObject(answer.value);
			answer.value.tooltipText = "{name}";
			answer.value.isHover = true
		} 
	}

	const restart = () => {
		score.value = 0;
		lifes.value = 5;
		gameOver.value = false;
		map.goHome();
		getRandomCountry()
		// map.setState("default")	
	}

	const checkIfGameIsOver = () => {
		if(lifes.value === 0) {			
			restart()	
			return gameOver.value = true
		}
	}
	
	function makeGuess(event : any) {
		
		checkIfCorrectAnswer(event)
		
		let answer : Ref<any> = ref();

		showCorrectAnswer(answer)

		checkIfGameIsOver()
		
	}
		

	
	const zoomOut = () => {
		map.goHome();
	}
	
	return {
		randomCountry,
		countryList,
		countryNamdAndId,
		score,
		lifes,
		gameOver,
		restart,
		zoomOut
		
	}
}    
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.container{
	position: relative;
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
  position: relative;
}

.zoomout {
	position: absolute;
	top: 0;
	right: 8rem;
	z-index: 2;
	/* width: 5.5rem;
	height: 2.5rem;
	border: none;
	cursor: pointer;
	border-radius: 4px;
	background-color: #349EFF;
	font-weight: 600; */
	
}

.restart, .zoomout {
	width: 5.5rem;
	height: 2.5rem;
	border: none;
	cursor: pointer;
	border-radius: 4px;
	background-color: #349EFF;
	font-weight: 600;
}

.gameover {
	width: 100%;
	height: 580px;
	z-index: 1;
	position: absolute;
	background-color: rgba(54, 146, 250, 0.226);
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
}
h1 {
	margin-bottom: 1rem;
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
