<template>
  <div class="weather__box">
    <div class="weather__sky">
      <input
        type="text"
        v-model="cityName"
        @keyup.enter="checkWeather"
        placeholder="enter city name"
      />
      <div class="tempOption" v-if="showResult">
        <label
          ><input
            @change="changeUnits"
            v-model="units"
            type="radio"
            v-bind:value="'imperial'"
          />°F</label
        >
        <label
          >°C<input
            @change="changeUnits"
            v-model="units"
            type="radio"
            v-bind:value="'metric'"
        /></label>
      </div>
      <p :class="{ active: errorInfo }">Try again, enter correct city name</p>
      <div
        class="weather__sky--first"
        :style="{
          transform: `translateX(-50%) rotate(${countRotate(
            6,
            18,
            -65,
            65
          )}deg)`
        }"
      ></div>
      <div class="weather__sky--second"></div>
      <div class="weather__sky--third"></div>
      <div class="weather__sky--fourth"></div>
    </div>
    <div class="weather__box-search">
      <button @click="checkWeather">search</button>
    </div>

    <div v-if="showResult" class="weather__box-showResult">
      <div class="weather_box-showResult__localisationInfo">
        <p>
          <svg
            aria-hidden="true"
            focusable="false"
            data-prefix="far"
            data-icon="clock"
            class="svg-inline--fa fa-clock fa-w-16"
            role="img"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 512 512"
          >
            <path
              fill="currentColor"
              d="M256 8C119 8 8 119 8 256s111 248 248 248 248-111 248-248S393 8 256 8zm0 448c-110.5 0-200-89.5-200-200S145.5 56 256 56s200 89.5 200 200-89.5 200-200 200zm61.8-104.4l-84.9-61.7c-3.1-2.3-4.9-5.9-4.9-9.7V116c0-6.6 5.4-12 12-12h32c6.6 0 12 5.4 12 12v141.7l66.8 48.6c5.4 3.9 6.5 11.4 2.6 16.8L334.6 349c-3.9 5.3-11.4 6.5-16.8 2.6z"
            ></path>
          </svg>
          {{ showTime }}
        </p>
        <p>
          {{ apiCityName.name }}
          {{ apiCityName.country }}
        </p>
      </div>
      <div class="weather__box-showResult__mainInfo">
        <div class="weather__box-showResult__mainInfo-general">
          <img
            :src="
              `http://openweathermap.org/img/wn/${displayWeatherInfo.icon}@2x.png`
            "
          />
          <p class="generalDescription">
            {{ displayWeatherInfo.generalDescription }}
          </p>
          <span> {{ mainWeatherInfo.temp | addUnits(units) }}</span>
        </div>
        <div class="weather__box-showResult__mainInfo-temperatureInfo">
          <p>H {{ mainWeatherInfo.tempMax | addUnits(units) }}</p>
          <p>L {{ mainWeatherInfo.tempMin | addUnits(units) }}</p>
          <p>Feels {{ mainWeatherInfo.feelsLike | addUnits(units) }}</p>
        </div>
        <div class="weather__box-showResult__mainInfo-windInfo">
          <p>
            <svg
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="cloud"
              class="svg-inline--fa fa-cloud fa-w-20"
              role="img"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 640 512"
            >
              <path
                fill="  #7ac8d4"
                d="M537.6 226.6c4.1-10.7 6.4-22.4 6.4-34.6 0-53-43-96-96-96-19.7 0-38.1 6-53.3 16.2C367 64.2 315.3 32 256 32c-88.4 0-160 71.6-160 160 0 2.7.1 5.4.2 8.1C40.2 219.8 0 273.2 0 336c0 79.5 64.5 144 144 144h368c70.7 0 128-57.3 128-128 0-61.9-44-113.6-102.4-125.4z"
              ></path></svg
            >{{ cloudsWeatherInfo.clouds }}%
          </p>

          <p>{{ mainWeatherInfo.pressure }} hpa</p>
          <p>
            <svg
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="wind"
              class="svg-inline--fa fa-wind fa-w-16"
              role="img"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 512"
            >
              <path
                fill="#7ac8d4"
                d="M156.7 256H16c-8.8 0-16 7.2-16 16v32c0 8.8 7.2 16 16 16h142.2c15.9 0 30.8 10.9 33.4 26.6 3.3 20-12.1 37.4-31.6 37.4-14.1 0-26.1-9.2-30.4-21.9-2.1-6.3-8.6-10.1-15.2-10.1H81.6c-9.8 0-17.7 8.8-15.9 18.4 8.6 44.1 47.6 77.6 94.2 77.6 57.1 0 102.7-50.1 95.2-108.6C249 291 205.4 256 156.7 256zM16 224h336c59.7 0 106.8-54.8 93.8-116.7-7.6-36.2-36.9-65.5-73.1-73.1-55.4-11.6-105.1 24.9-114.9 75.5-1.9 9.6 6.1 18.3 15.8 18.3h32.8c6.7 0 13.1-3.8 15.2-10.1C325.9 105.2 337.9 96 352 96c19.4 0 34.9 17.4 31.6 37.4-2.6 15.7-17.4 26.6-33.4 26.6H16c-8.8 0-16 7.2-16 16v32c0 8.8 7.2 16 16 16zm384 32H243.7c19.3 16.6 33.2 38.8 39.8 64H400c26.5 0 48 21.5 48 48s-21.5 48-48 48c-17.9 0-33.3-9.9-41.6-24.4-2.9-5-8.7-7.6-14.5-7.6h-33.8c-10.9 0-19 10.8-15.3 21.1 17.8 50.6 70.5 84.8 129.4 72.3 41.2-8.7 75.1-41.6 84.7-82.7C526 321.5 470.5 256 400 256z"
              ></path></svg
            >{{ windWeatherInfo.speed | addUnitsWind(units) }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "WeatherApp",
  data() {
    return {
      cityName: "",
      displayWeatherInfo: {},
      mainWeatherInfo: {},
      windWeatherInfo: "",
      rainWeatherInfo: "",
      cloudsWeatherInfo: "",
      apiCityName: "",
      showResult: false,
      errorInfo: false,
      units: "metric",
      deg: "20"
    };
  },
  computed: {
    showTime() {
      let currentDate = new Date();
      let minutes =
        currentDate.getMinutes().toString().length == 2
          ? currentDate.getMinutes()
          : "0" + currentDate.getMinutes();
      return currentDate.getHours() + ":" + minutes;
    }
  },
  filters: {
    addUnits(value, units) {
      if (value) {
        if (units === "metric") {
          return Math.round(value) + "°C";
        } else {
          return Math.round(value) + "°F";
        }
      }
    },
    addUnitsWind(value, units) {
      if (value) {
        if (units === "metric") {
          return Math.round(value) + "m/s";
        } else {
          return Math.round(value) + "mph";
        }
      }
    }
  },
  methods: {
    countRotate(in_min, in_max, out_min, out_max) {
      let currentHours = new Date().getHours();

      if (currentHours >= 6 && currentHours <= 18) {
        return (
          ((currentHours - in_min) * (out_max - out_min)) / (in_max - in_min) +
          out_min
        );
      } else {
        return 120;
      }
    },
    changeUnits() {
      this.checkWeather();
    },
    checkWeather() {
      if (this.cityName.length == 0) {
        this.errorInfo = true;
        this.showResult = false;
      } else {
        this.showResult = true;
        this.errorInfo = false;
        this.axios
          .get(
            ` https://api.openweathermap.org/data/2.5/weather?q=${this.cityName}&units=${this.units}&appid=4c9dac83626fd5803e4204152fb413e7`
          )
          .then(response => {
            let res = response.data;
            this.mainWeatherInfo = {
              temp: Math.round(res.main.temp),
              feelsLike: res.main.feels_like,
              tempMin: res.main.temp_min,
              tempMax: res.main.temp_max,
              pressure: res.main.pressure
            };
            this.windWeatherInfo = {
              speed: res.wind.speed
            };
            this.rainWeatherInfo = {
              rain: res.rain
            };
            this.cloudsWeatherInfo = {
              clouds: res.clouds.all
            };
            this.apiCityName = {
              name: res.name,
              country: res.sys.country
            };
            res.weather.forEach(element => {
              this.displayWeatherInfo = {
                generalDescription: element.main,
                description: element.description,
                icon: element.icon
              };
            });
          })
          .catch(err => {
            console.log("błąd");
            this.errorInfo = true;
            this.showResult = false;
          });
      }
    }
  }
};
</script>

<style scoped lang="scss">
.weather__box {
  width: 100%;
  height: 100vh;

  display: flex;
  flex-direction: column;
  align-items: center;

  .weather__sky {
    width: 100%;
    min-height: 45vh;

    background-color: #73bbc7;
    position: relative;
    overflow: hidden;
    ::placeholder {
      color: #4e8892;
    }
    input[type="text"] {
      position: absolute;
      width: 80%;
      top: 45%;
      left: 50%;
      transform: translateX(-50%);
      z-index: 5;
      padding: 11px 15px;
      background-color: rgba(255, 255, 255, 0.3);
      border: none;
        text-align: center;
      outline: none;
      color: #4e8892;
      border-radius: 4px;
      font-size: 2rem;
      -webkit-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      -moz-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.25);
    }
    p {
      visibility: hidden;
    }
    .active {
      visibility: visible;
      color: #d0edf1;
      font-weight: bold;
      width: 100%;
      left: 50%;
      top: 10%;
      transform: translateX(-50%);
      position: absolute;
      z-index: 5;
    }
    .weather__sky--first {
      width: 130vw;
      padding-top: 130%;
      background-color: #7ac8d4;
      position: absolute;
      border-radius: 50%;
      top: 10%;
      left: 50%;

      &:after {
        content: "";
        width: 50px;
        height: 50px;
        position: absolute;
        top: 0px;
        left: 50%;
        background: yellow;
        border-radius: 50%;
        z-index: 8888;
        transform: translateX(-50%) translateY(-50%);
      }
    }
    .weather__sky--second {
      width: 110vw;
      padding-top: 110%;
      background-color: #95d4d9;
      position: absolute;
      border-radius: 50%;
      top: 30%;
      left: 50%;
      transform: translateX(-50%);
      z-index: 2;
    }
    .weather__sky--third {
      width: 90vw;
      padding-top: 90%;
      background-color: #b5e2e8;
      position: absolute;
      border-radius: 50%;
      top: 50%;
      left: 50%;
      transform: translateX(-50%);
      z-index: 3;
    }
    .weather__sky--fourth {
      width: 65vw;
      padding-top: 70%;
      background-color: #d0edf1;
      position: absolute;
      border-radius: 50%;
      top: 70%;
      left: 50%;
      transform: translateX(-50%);
      z-index: 4;
    }
  }
  .weather__box-search {
    display: flex;
    justify-content: center;
    position: relative;

    button {
      position: absolute;
      height: 4rem;
      width: 4rem;
      bottom: 100%;
      z-index: 6;
      border-radius: 50%;
      transform: translateY(50%);
      outline: none;
      border: none;
      font-family: "Comfortaa", cursive;
      background-color: #7ac8d4;
      color: white;
      -webkit-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      -moz-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
    }
  }

  .tempOption {
    display: flex;
    justify-content: space-around;
    width: 100%;
    position: absolute;
    top: 85%;
    z-index: 6;

    input[type="radio"] {
      outline: none;
      -webkit-appearance: none;
      -moz-appearance: none;
      appearance: none;
      -webkit-print-color-adjust: exact;
      color-adjust: exact;
      display: inline-block;
      vertical-align: middle;
      background-origin: border-box;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
      flex-shrink: 0;
      border-radius: 100%;
      height: 1rem;
      width: 1rem;
      color: #7ac8d4;
      background-color: #fff;
      border-color: #e2e8f0;
      border-width: 1px;
    }
    input[type="radio"]:checked {
      outline: none;
      background-image: url("data:image/svg+xml,%3csvg viewBox='0 0 16 16' fill='white' xmlns='http://www.w3.org/2000/svg'%3e%3ccircle cx='8' cy='8' r='3'/%3e%3c/svg%3e");
      border-color: transparent;
      background-color: currentColor;
      background-size: 100% 100%;
      background-position: center;
      background-repeat: no-repeat;
    }
  }
  .weather__box-showResult {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    .weather_box-showResult__localisationInfo {
      width: 100%;
      display: flex;
      justify-content: space-between;
      :first-child {
        padding-left: 0.5rem;
      }
      :last-child {
        padding-right: 0.5rem;
      }
      p {
        display: flex;
        align-items: center;

        svg {
          width: 2rem;
        }
      }
    }
    .weather__box-showResult__mainInfo {
      display: flex;
      width: 100%;
      justify-content: center;
      flex-direction: column;
      align-items: center;

      .weather__box-showResult__mainInfo-general {
        width: 50%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        img {
          width: 8rem;
          height: 7rem;
        }
        span {
          font-size: 3rem;
          font-family: "Comfortaa", cursive;
        }
      }
    }
    .weather__box-showResult__mainInfo-temperatureInfo {
      width: 80%;
      display: flex;
      flex-direction: row;
      justify-content: space-around;
      align-items: center;
    }
    .weather__box-showResult__mainInfo-windInfo {
      width: 80%;
      display: flex;
      flex-direction: row;
      justify-content: space-around;
      align-items: center;
      p {
        display: flex;
        align-items: center;
        color: #7ac8d4;

        svg {
          width: 2rem;
          margin-right: 0.5rem;
        }
      }
    }
  }
}

@media (min-width: 1000px) {

  .weather__box {
    .weather__sky{
      display: flex;
      justify-content: center;
    align-items: center;
      input[type="text"]{
        width: 42%;
      }
    .tempOption{
      width: 78%;
    }}
    .weather__box-showResult {
    width: 60%;
   align-items: center;
    .weather_box-showResult__localisationInfo{
      width: 70%;
    }
  }
}}
@media (max-width: 1000px) and (orientation: landscape) {
  .weather__box {
    height: 100%;
  }
}
</style>
