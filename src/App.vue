<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input v-model="query" type="text" placeholder="Search..." class="search-bar" @keypress="fetchWeather">
      </div>



      <div class="weather-wrap" v-if="typeof weather.name !== 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ formattedDate }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ weather.main.temp }}°C</div>
          <div class="weather">{{ weather.weather[0]?.main }} <img :src="icon_url" /></div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import moment from 'moment';

const api_key = 'YOUR_API_KEY';
1
const base_url = 'https://api.openweathermap.org/data/2.5/'
const icon_url = ref('');
const query = ref('')
const weather = ref({});
const currentDate = ref(new Date());

const formattedDate = computed(() => {
  return moment(currentDate.value).format('dddd DD MMM, YYYY');
});

onMounted(() => {
  setInterval(() => {
    currentDate.value = new Date();
  }, 1000);

  if ("geolocation" in navigator) {
    navigator.geolocation.getCurrentPosition(position => {
      fetchWeatherOnLoad(position.coords.latitude, position.coords.longitude)
    });
  } else {
    console.error("Geolocation is not supported by this browser.");
  }
});


// fetch weather
const fetchWeather = (e) => {
  if (e.key == 'Enter') {

    fetch(`${base_url}weather?q=${query.value}&units=metric&APPID=${api_key}`)
      .then(res => {
        return res.json();
      })
      .then((res) => {
        weather.value = res
        icon_url.value = 'http://openweathermap.org/img/w/' + weather.value.weather[0]?.icon + '.png';
      })
  }
}

// fetch weather on page load
const fetchWeatherOnLoad = (lat, long) => {
  fetch(`${base_url}weather?lat=${lat}&lon=${long}&appid=${api_key}`)
    .then(res => {
      return res.json();
    })
    .then((res) => {
      weather.value = res
      icon_url.value = 'http://openweathermap.org/img/w/' + weather.value.weather[0]?.icon + '.png';
    })
}
</script>






<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'montserrat', sans-serif;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/ice.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  padding: 2%;
}

.search-box .search-bar {
  width: 100%;
  margin-bottom: 30px;
  font-size: 1.4rem;
  padding: 17px;
  border: none;
  outline: none;
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 4px;
  color: #fff;
  box-shadow: 5px 5px 15px #000;
}

.weather-wrap {
  text-align: center;
  color: #fff;
}

.weather-wrap .location {
  font-size: 2rem;
  color: #fff;
  font-weight: 500;
  text-shadow: 0px 2px rgba(100, 100, 100, 0.7);
}

.weather-wrap .date {
  margin-top: 10px;
  font-size: 1.7rem;
  font-style: italic;
}

.weather-box {
  text-align: center;
  display: inline-block;
}

.weather-box .temp {
  font-size: 6rem;
  font-weight: bolder;
  font-style: italic;

  padding: 7px;
  background: rgba(255, 255, 255, 0.1);
  width: 500px;
  border-radius: 5px;
  margin-top: 50px;
}

.weather-box .weather {
  font-size: 2rem;
  font-weight: 600;
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
</style>
