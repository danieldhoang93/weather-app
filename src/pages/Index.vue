<template>
  <q-page class="flex column" :class="backgroundColor">
    <div class="col q-pa-xl">
      <q-input v-model="search" 
      placeholder="Search" 
      @keyup.enter="getWeatherBySearch"
      dark 
      borderless>
        <template v-slot:before>
          <q-icon name="my_location" 
          @click="getLocation"/>
        </template>

        <template v-slot:append>
          <q-btn 
          round 
          dense 
          flat 
          icon="search" 
          @keyup.enter="getWeatherBySearch"/>
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
    <div class="col text-white text-center">
          <div class="text-h4 text-weight-light">
            {{weatherData.name}}
          </div>

          <div class="text-h6 text-weight-light">
            {{weatherData.weather[0].main}}
          </div>

          <div class="text-h1 text-weight-thin q-my-lg relative-position">
            <span>{{Math.round(weatherData.main.temp)}}</span>
            <span class="text-h4 relative-position degree">&deg;F</span>
          </div>
        </div>

        <div class="col text-center">
          <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon}@2x.png`">
        </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar<br>Weather
          </div>
          <q-btn 
          class="col" 
          flat
          @click="getLocation">
            <q-icon left size="3em" name="my_location" />
            <div>Find my location</div>
          </q-btn>
        
      </div>
    </template>

    <div class="col skyline">

    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lng: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '44eaf9faf46b5c4f281ea35b6a9b7f49',
      info: null
    }
  },
  computed: {
    backgroundColor() {
      if (this.weatherData) {
        //determine day or night depending on icon suffix
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation(){
      //show loading icon
      this.$q.loading.show()

      //get lat and lng
      navigator.geolocation.getCurrentPosition(
        position => {
        this.lat = position.coords.latitude;
        this.lng = position.coords.longitude;

        //run next method to get weather data
        this.getWeatherByCoords();
      })
    },
    getWeatherByCoords() {
      //show loading icon
      this.$q.loading.show()

      //call API for weather data and insert data into weatherData
      this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lng }&appid=${ this.apiKey }&units=imperial`)
      .then(response => {this.weatherData = response.data
      //hide loading icon
      this.$q.loading.hide()})
      .catch(e => {
        //catch errors
        console.log(response);
    });
    },
    getWeatherBySearch() {
      //show loading icon
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?q=${ this.search }&appid=${ this.apiKey }&units=imperial`)
      .then(response => {this.weatherData = response.data
      //hide loading icon
      this.$q.loading.hide()})
      .catch(e => {
        //catch errors
        console.log(response);
    });
    }
  }
}
</script>

<style lang="scss">
  .q-page {
  background: linear-gradient(to top, #e952af, #1d2671); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    &.bg-night {
      background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    }
    &.bg-day {
      background: linear-gradient(to bottom, #43c6ac, #f8ffae); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    }
  }

  .degreee {
    top: -44px;
  }

  .skyline {
    flex: 0 0 100px;
    //bakcground:
    background-size: contain;
    background-position: center bottom;
  }
</style>