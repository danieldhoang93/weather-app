<template>
  <q-page class="flex column elementToFadeInAndOut" :class="backgroundColor">
    <div :class="saturate">
      <img src="~/assets/sun.png" class="sun"/>
      <img src="~/assets/sun.png" class="sun1"/>
      <img src="~/assets/sun.png" class="sun2"/>
      <img src="~/assets/sun.png" class="sun3"/>
    </div>
    
    <div class="col q-pa-xl shadow">
      <q-input v-model="search" 
      placeholder="Search" 
      @keyup.enter="getWeatherBySearch"
      dark 
      borderless
      class="searchbar">
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
    <div class="col text-white text-center elementToFadeInAndOut shadow">
          <div class="text-h4 text-weight-light">
            {{weatherData.name}}
          </div>

          <div class="text-h6 text-weight-light">
            {{weatherData.weather[0].description}}
          </div>

          <div class="text-h1 text-weight-thin q-my-lg relative-position">
            <span>{{Math.round(weatherData.main.temp)}}</span>
            <span class="text-h4 relative-position degree">&deg;F</span>
          </div>
        </div>

        <div class="col text-center icon shadow2">
          <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon}@2x.png`">
        </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white shadow">
        <div class="col text-h2 text-weight-thin">
          The<br>Weather
          </div>
      </div>
    </template>

    <div class="col backgroundPicture">

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
    },
    saturate() {
      var d = new Date().getHours();//want to base saturation and colors off the locations time
      if (this.weatherData) {
       return this.weatherData.weather[0].icon.endsWith('n') ? 'unsaturate rotatingSun' : 'saturate rotatingSun' 
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
    background: linear-gradient(to bottom, #5433ff, #20bdff, #a5fecb); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }
}

.rotatingSun {
  position: absolute;
  margin: auto;
  top: 400px;
  left: 0;
  right: 0;
}

.sun {
  width:90vw;
  max-width: 750px;
  position: absolute;
  margin: auto;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  opacity: .3;
  animation-name: spin;
  animation-duration: 30000ms;
  animation-iteration-count: infinite;
  animation-timing-function: linear; 
}

.sun1 {
  width:82vw;
  max-width:680px;
  position: absolute;
  margin: auto;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  opacity: .3;
  animation-name: spin;
  animation-duration: 50000ms;
  animation-iteration-count: infinite;
  animation-timing-function: linear; 
}

.sun2 {
  width:70vw;
  max-width:580px;
  position: absolute;
  margin: auto;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  opacity: .3;
  animation-name: spin;
  animation-duration: 70000ms;
  animation-iteration-count: infinite;
  animation-timing-function: linear;  
}

.sun3 {
  width:55vw;
  max-width:350px;
  position: absolute;
  margin: auto;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  opacity: .3;
  animation-name: spin;
  animation-duration: 70000ms;
  animation-iteration-count: infinite;
  animation-timing-function: linear; 
}

.saturate {
  //0-300%
  filter: saturate(200%);
}

.unsaturate {
  //0-200%
  filter: saturate(30%);
}

.searchbar {
  max-width:90%;
  left:50%;
  top:20vh;
  position:absolute;
  transform:translate(-50%,-50%);
  text-align:center;
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

.icon {
  max-width:90%;
  left:50%;
  top:70%;
  position:absolute;
  transform:translate(-50%,-50%);
  text-align:center;
}

.shadow {
  filter: drop-shadow(8px 8px 5px rgba(0, 0, 0, 0.952));
}

.shadow2 {
  filter: drop-shadow(8px 8px 5px rgba(0, 0, 0, 0.352));
}

.elementToFadeInAndOut {
    animation: fadeinout 5s linear forwards;
}	

@keyframes spin {
    from {
        transform:rotate(0deg);
    }
    to {
        transform:rotate(360deg);
    }
}

@keyframes fadeinout {
    0% { opacity: 0; }
    30% { opacity: 1; }
    90% { opacity: 1; }
}

@keyframes spin {
    from {
        transform:rotate(0deg);
    }
    to {
        transform:rotate(360deg);
    }
}
</style>