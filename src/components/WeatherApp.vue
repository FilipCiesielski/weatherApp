<template>
  <div class="wheather__box">
    <div class="wheather__box-search">
      <input
        type="text"
        v-model="cityName"
        @keyup.enter="checkWheather"
        placeholder="enter city name"
      />
      <button @click="checkWheather">search</button>
      <p :class="{ active: errorInfo }">Try again, enter correct city name</p>
    </div>

    <div v-if="showResult" class="wheather__box-showResult">
      <div class="wheather__box-showResult__mainInfo">
        <div>
          <p class="generalDescription">
            {{ displayWeatherInfo.generalDescription }}
          </p>
          <img
            :src="
              `http://openweathermap.org/img/wn/${displayWeatherInfo.icon}@2x.png`
            "
          />
        </div>
        <div>
          <span> {{ mainWeatherInfo.temp | addUnits }}</span>
          <p>
            {{ apiCityName.name }}
            {{ apiCityName.country }}
          </p>
        </div>
      </div>
      <div class="wheather__box-showResult__extraInfo">
        <div class="wheather__box-showResult-temp">
          <div class="wheather__box-showResult-temp-items">
            <p>Feels like:</p>
            {{ mainWeatherInfo.feelsLike | addUnits }}
          </div>

          <div class="wheather__box-showResult-temp-items">
            <p>Temp. min:</p>
            {{ mainWeatherInfo.tempMin | addUnits }}
          </div>
          <div class="wheather__box-showResult-temp-items">
            <p>Temp. max:</p>
            {{ mainWeatherInfo.tempMax | addUnits }}
          </div>
        </div>

        <div class="wheather__box-showResult-wind">
          <div class="wheather__box-showResult-wind-items">
            <p>Clouds:</p>
            {{ cloudsWeatherInfo.clouds }}%
          </div>
          <div class="wheather__box-showResult-wind-items">
            <p>Pressure:</p>
            {{ mainWeatherInfo.pressure }} hpa
          </div>
          <div class="wheather__box-showResult-wind-items">
            <p>Wind speed:</p>
            {{ windWeatherInfo.speed }}m/s
          </div>
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
      errorInfo: false
    };
  },
  filters: {
    addUnits(value) {
      if (value) {
        return Math.round(value) + "°C";
      }
    }
  },
  methods: {
    checkWheather() {
      if (this.cityName.length == 0) {
        this.errorInfo = true;
        this.showResult = false;
      } else {
        this.showResult = true;
        this.errorInfo = false;
        this.axios
          .get(
            ` https://api.openweathermap.org/data/2.5/weather?q=${this.cityName}&units=metric&appid=4c9dac83626fd5803e4204152fb413e7`
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
.wheather__box {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 0 1rem 0 1rem;
  background: rgba(98, 140, 245, 1);
  background: -moz-linear-gradient(
    top,
    rgba(98, 140, 245, 1) 0%,
    rgba(77, 149, 245, 0.8) 5%,
    #00b5f7d9 23%,
    rgba(217, 234, 242, 0.96) 63%,
    rgba(239, 239, 242, 0.96) 67%,
    rgba(239, 239, 242, 0.96) 68%,
    rgba(73, 113, 194, 0.94) 87%,
    rgba(20, 73, 179, 0.94) 93%
  );
  background: -webkit-gradient(
    left top,
    left bottom,
    color-stop(0%, rgba(98, 140, 245, 1)),
    color-stop(5%, rgba(77, 149, 245, 0.8)),
    color-stop(23%, rgba(0, 181, 247, 0.85)),
    color-stop(63%, rgba(217, 234, 242, 0.96)),
    color-stop(67%, rgba(239, 239, 242, 0.96)),
    color-stop(68%, rgba(239, 239, 242, 0.96)),
    color-stop(87%, rgba(73, 113, 194, 0.94)),
    color-stop(93%, rgba(20, 73, 179, 0.94))
  );
  background: -webkit-linear-gradient(
    top,
    rgba(98, 140, 245, 1) 0%,
    rgba(77, 149, 245, 0.8) 5%,
    rgba(0, 181, 247, 0.85) 23%,
    rgba(217, 234, 242, 0.96) 63%,
    rgba(239, 239, 242, 0.96) 67%,
    rgba(239, 239, 242, 0.96) 68%,
    rgba(73, 113, 194, 0.94) 87%,
    rgba(20, 73, 179, 0.94) 93%
  );
  background: -o-linear-gradient(
    top,
    rgba(98, 140, 245, 1) 0%,
    rgba(77, 149, 245, 0.8) 5%,
    rgba(0, 181, 247, 0.85) 23%,
    rgba(217, 234, 242, 0.96) 63%,
    rgba(239, 239, 242, 0.96) 67%,
    rgba(239, 239, 242, 0.96) 68%,
    rgba(73, 113, 194, 0.94) 87%,
    rgba(20, 73, 179, 0.94) 93%
  );
  background: -ms-linear-gradient(
    top,
    rgba(98, 140, 245, 1) 0%,
    rgba(77, 149, 245, 0.8) 5%,
    rgba(0, 181, 247, 0.85) 23%,
    rgba(217, 234, 242, 0.96) 63%,
    rgba(239, 239, 242, 0.96) 67%,
    rgba(239, 239, 242, 0.96) 68%,
    rgba(73, 113, 194, 0.94) 87%,
    rgba(20, 73, 179, 0.94) 93%
  );
  background: linear-gradient(
    to bottom,
    rgba(98, 140, 245, 1) 0%,
    rgba(77, 149, 245, 0.8) 5%,
    rgba(0, 181, 247, 0.85) 23%,
    rgba(217, 234, 242, 0.96) 63%,
    rgba(239, 239, 242, 0.96) 67%,
    rgba(239, 239, 242, 0.96) 68%,
    rgba(73, 113, 194, 0.94) 87%,
    rgba(20, 73, 179, 0.94) 93%
  );
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#628cf5', endColorstr='#1449b3', GradientType=0 );
  ::placeholder {
    color: white;
  }
  .wheather__box-search {
    margin-bottom: 2rem;
    padding-top: 2rem;

    input {
      border: none;
      height: 2rem;
      text-align: center;
      outline: none;
      font-family: "Comfortaa", cursive;
      background-color: transparent;
      color: white;
      border: 1px solid white;
      border-radius: 5%;
      -webkit-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      -moz-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
    }
    button {
      height: 2rem;
      padding: 0 1rem 0 1rem;
      border: 1px solid white;
      outline: none;
      font-family: "Comfortaa", cursive;
      border-radius: 5%;
      margin-left: 2rem;
      background-color: white;
      color: #00b5f7d9;
      -webkit-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      -moz-box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      box-shadow: 0px 6px 8px -4px rgba(0, 0, 0, 0.75);
      &:hover {
        background-color: transparent;
        color: white;
      }
    }
    p {
      margin-top: 2rem;
      display: none;
    }
    .active {
      display: flex;
      color: red;
    }
  }
  .searchView {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 405px;
    font-size: 3rem;
    color: grey;
  }
  .wheather__box-showResult {
    width: 100%;
    display: flex;
    flex-direction: column;
    .wheather__box-showResult__mainInfo {
      display: flex;
      justify-content: space-between;
      margin-top: 1.5rem;
      div {
        width: 50%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin-bottom: 2rem;
        img {
          width: 7rem;
          height: 7rem;
          border: 1px solid white;
          border-radius: 50%;
          -webkit-box-shadow: -9px 10px 8px -4px rgba(0, 0, 0, 0.75);
          -moz-box-shadow: -9px 10px 8px -4px rgba(0, 0, 0, 0.75);
          box-shadow: -9px 10px 8px -4px rgba(0, 0, 0, 0.75);
        }
        span {
          font-size: 4rem;
          font-family: "Comfortaa", cursive;
        }
      }
    }
    .wheather__box-showResult__extraInfo {
      display: flex;
      justify-content: space-between;
      width: 100%;
      font-size: 12px;
      margin-top: 3.5rem;

      .wheather__box-showResult-temp {
        width: 50%;
        display: flex;
        flex-direction: column;

        .wheather__box-showResult-temp-items {
          width: 80%;
          display: flex;
          align-items: center;
          -webkit-box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);
          -moz-box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);
          box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);

          border-radius: 5px;
          margin-bottom: 5px;

          color: white;

          p {
            padding-left: 5px;
            margin-right: 5px;
          }
        }
      }
      .wheather__box-showResult-wind {
        width: 50%;
        display: flex;
        flex-direction: column;
        color: white;
        .wheather__box-showResult-wind-items {
          width: 100%;
          display: flex;
          align-items: center;
          -webkit-box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);
          -moz-box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);
          box-shadow: -5px 4px 20px -4px rgba(0, 0, 0, 0.75);
          border-radius: 5px;
          margin-bottom: 5px;
          p {
            margin-right: 5px;
            padding-left: 5px;
          }
        }
      }
    }
  }
}
@media (min-width: 1000px) {
  .wheather__box {
    .wheather__box-showResult {
      width: 50%;
      align-items: center;
      .wheather__box-showResult__mainInfo {
        div {
          margin-left: 2rem;
          margin-right: 2rem;
        }
      }
      .wheather__box-showResult__extraInfo {
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .wheather__box-showResult-temp,
        .wheather__box-showResult-wind {
          flex-direction: row;
          .wheather__box-showResult-temp-items,
          .wheather__box-showResult-wind-items {
            color: grey;

            justify-content: center;
          }
        }
      }
    }
  }
}
@media (max-width: 1000px) and (orientation: landscape) {
  .wheather__box {
    height: 100%;
  }
}
</style>
