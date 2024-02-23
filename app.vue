<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input
            type="text"
            class="search-bar"
            placeholder="Search..."
            v-model="query"
            @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].description }}
            <img :src="icon" alt="weather icon"/>
          </div>
          <div class="weather">влажность {{ weather.main.humidity }}</div>
          <div class="weather">скорость ветра {{ weather.wind.speed }} м/с</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import {ref} from "vue"

export default {
  name: 'app',
  setup() {
    const api_key = 'f569230abba06503640540b4016701a6',
        url_base = 'https://api.openweathermap.org/data/2.5/',
        url_icons = 'http://openweathermap.org/img/w/',
        query = ref(''),
        weather = ref({})

    const fetchWeather = (e) => {
      if (e.key == "Enter") {
        fetch(`${url_base}weather?q=${query.value}&units=metric&APPID=${api_key}&lang=ru`)
            .then(res => {
              return res.json();
            }).then(json => {
          weather.value = json
        })
      }
    }
    const icon = computed(() => weather.value.weather ? url_icons + weather.value.weather[0].icon + ".png" : '')
    const dateBuilder = () => {
      let weekday = new Date().toLocaleString('ru',
          {
            weekday: 'long',
            day: 'numeric',
            month: 'long',
            year: 'numeric'
          })
      return `${weekday}`;
    }

    return {
      api_key,
      url_base,
      query,
      weather,
      fetchWeather,
      dateBuilder,
      icon
    }
  }
}

</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 24px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>