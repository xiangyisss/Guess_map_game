<template>
  <div class="container">
    <div v-if="answer" class="correct_answer">{{ answer }}</div>
    <div class="gameover" v-if="gameOver">
      <h1>Game over</h1>
      <button class="restart" @click="restart">Restart</button>
    </div>
    <div class="score_life">
      <h4 class="score">Score : {{ score }}</h4>
      <h4>Lifes : {{ lifes }}</h4>
    </div>

    <div class="mapcontainer">
      <div
        id="chartdiv"
        @touchstart="trackTouchMovement"
        @click="trackMouseMovement"
      >
        <div ref="selectedCountry">
          <div v-if="slectedCountryName" class="selected_country_name">
            {{ slectedCountryName }}
          </div>
        </div>
      </div>
    </div>
    <div class="btnbox">
      <button class="zoomout" @click="zoomOut">Rezoom</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, Ref } from "vue";
import * as am4core from "@amcharts/amcharts4/core";
import * as am4maps from "@amcharts/amcharts4/maps";
import am4geodata_worldLow from "@amcharts/amcharts4-geodata/worldLow";

interface country {
  countryId: string;
  countryName: string;
}

interface position {
  top: string;
  left: string;
}

export default defineComponent({
  name: "WorldMap",
  components: {},
  setup(props, { emit }) {
    const map = am4core.create("chartdiv", am4maps.MapChart);
    map.draggable = false;
    map.geodata = am4geodata_worldLow;
    map.geodataSource.url = "/path/to/myCustomMap.json";
    map.projection = new am4maps.projections.Mercator();
    const WorldMap = map.series.push(new am4maps.MapPolygonSeries());
    WorldMap.exclude = ["AQ", "UM-FQ", "TV"];
    WorldMap.useGeodata = true;
    WorldMap.calculateVisualCenter = true;
    const worldMapTemplate = WorldMap.mapPolygons.template;
    worldMapTemplate.fill = am4core.color("#41729F");
    const hoverState = worldMapTemplate.states.create("hover");
    hoverState.properties.fill = am4core.color("#5885AF");
    worldMapTemplate.tooltipPosition = "fixed";
    const countryList: Ref<any[]> = ref([]);
    countryList.value = WorldMap.data;
    const countryNamdAndCountryId: Ref<any[]> = ref([]);
    const randomCountry: Ref<country> = ref({
      countryId: " ",
      countryName: " ",
    });
    const score: Ref<number> = ref(0);
    const lifes: Ref<number> = ref(5);
    const answer: Ref<any> = ref();
    const slectedCountryName: Ref<any> = ref();
    const gameOver: Ref<boolean> = ref(false);
    const count: Ref<number> = ref(1);
    const mousePosition: Ref<position> = ref({
      top: " ",
      left: " ",
    });
    const getRandomCountry = () => {
      const randomNumber = Math.floor(
        Math.random() * countryNamdAndCountryId.value.length
      );
      const countryNameandCountryCode =
        countryNamdAndCountryId.value[randomNumber];
      randomCountry.value = {
        countryId: countryNameandCountryCode.countryId,
        countryName: countryNameandCountryCode.countryName,
      };
      emit("randomCountryName", randomCountry.value);
    };
    setTimeout(() => {
      for (const item of countryList.value) {
        const countryInfo = {
          countryName: item.name,
          countryId: item.id,
        };
        countryNamdAndCountryId.value.push(countryInfo);
      }
      getRandomCountry();
    }, 0);
    worldMapTemplate.events.on("hit", makeGuess);
    const trackTouchMovement = (touched: any) => {
      let touch = touched.touches[0];
      mousePosition.value = {
        left: touch.pageX,
        top: touch.pageY,
      };
      return mousePosition.value;
    };
    const trackMouseMovement = (clicked: any) => {
      mousePosition.value = {
        left: clicked.clientX,
        top: clicked.clientY,
      };
      return mousePosition.value;
    };
    const selectedCountry = ref(null);
    onMounted(() => {
      selectedCountry.value.style.position = "absolute";
      selectedCountry.value.style.left = 0 + "px";
      selectedCountry.value.style.top = 0 + "px";
      selectedCountry.value.style.zIndex = "0";
      selectedCountry.value.style.transform = "translate(0, 0)";
    });
    const getPosition = () => {
      selectedCountry.value.style.position = "absolute";
      selectedCountry.value.style.left = mousePosition.value.left + "px";
      selectedCountry.value.style.top = mousePosition.value.top + "px";
      selectedCountry.value.style.zIndex = "3";
      selectedCountry.value.style.transform = "translate(-50%, -50%)";
    };
    function showSelectedCountryName(country: any) {
      if (
        randomCountry.value.countryName !=
        country.target.dataItem.dataContext.name
      ) {
        setTimeout(() => {
          slectedCountryName.value = country.target.dataItem.dataContext.name;
          getPosition();
        });
        setTimeout(() => {
          slectedCountryName.value = "";
        }, 500);
      }
    }
    const checkIfCorrectAnswer = (country: any) => {
      if (
        randomCountry.value.countryName ===
        country.target.dataItem.dataContext.name
      ) {
        country.target.fill = am4core.color("#3D550C");
        getRandomCountry();
        answer.value = "";
        zoomOut();
        return (score.value += 1);
      }
      lifes.value -= 1;
      showSelectedCountryName(country);
      country.target.fill = am4core.color("#F51720");
    };
    const showCorrectAnswer = (answer: any) => {
      if (lifes.value === 2) {
        if (count.value === 1) {
          setTimeout(() => {
            answer.value = randomCountry.value.countryName;
            const correctCountryMap = ref();
            correctCountryMap.value = WorldMap.getPolygonById(
              randomCountry.value.countryId
            );
            correctCountryMap.value.series.chart.zoomToMapObject(
              correctCountryMap.value
            );
          }, 500);
        }
        count.value = 0;
      }
    };

    const restart = () => {
      score.value = 0;
      lifes.value = 5;
      count.value = 1;
      answer.value = "";
      gameOver.value = false;
      map.goHome();
      getRandomCountry();
    };

    const checkIfGameIsOver = () => {
      if (lifes.value === 0) {
        gameOver.value = true;
      }
    };

    function makeGuess(event: any) {
      checkIfCorrectAnswer(event);

      showSelectedCountryName(event);

      showCorrectAnswer(answer);

      checkIfGameIsOver();
    }

    const zoomOut = () => {
      map.goHome();
    };

    return {
      answer,
      randomCountry,
      countryList,
      countryNamdAndCountryId,
      score,
      lifes,
      gameOver,
      restart,
      zoomOut,
      slectedCountryName,
      trackMouseMovement,
      selectedCountry,
      trackTouchMovement,
    };
  },
});
</script>

<style scoped>
.selected_country_name {
  padding: 4px;
  background-color: rgba(226, 226, 226, 0.692);
  border-radius: 5px;
  font-size: 0.75rem;
}
.score_life {
  display: flex;
  width: 20%;
  position: absolute;
  top: 0;
  right: 0;
  z-index: 1;
}
.correct_answer {
  position: absolute;
  top: 50%;
  left: 50%;
  padding: 10px;
  background-color: rgba(255, 255, 255, 0.438);
  border-radius: 5px;
  z-index: 1;
}
.container {
  position: relative;
}

.score {
  margin-right: 1rem;
}

.mapcontainer {
  width: 100%;
  height: 80vh;
  margin-top: 1.5rem;
}
#chartdiv {
  width: 80%;
  height: 100%;
  margin: 0 auto;
}
.zoomout {
  position: absolute;
  bottom: 1rem;
  right: 8rem;
  z-index: 2;
}
.restart,
.zoomout {
  width: 5.5rem;
  height: 2.5rem;
  border: none;
  cursor: pointer;
  border-radius: 4px;
  background-color: #daedff;
  font-weight: 600;
}
.gameover {
  width: 100%;
  min-height: 80vh;
  z-index: 1;
  position: absolute;
  background-color: rgba(199, 217, 238, 0.432);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
@media (max-width: 850px) {
  .score_life {
    width: 100%;
    display: flex;
    justify-content: center;
  }
  .zoomout {
    position: static;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .btnbox {
    width: 100%;
    height: auto;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
@media (max-width: 600px) {
  .mapcontainer {
    overflow: hidden;
  }
  #chartdiv {
    transform: scale(2, 2);
    position: relative;
    z-index: 0;
  }
}
</style>
