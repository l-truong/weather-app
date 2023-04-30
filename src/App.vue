9 lines (145 sloc) 3.6 KB
<template>  
  <div id="contain" :class="typeof weather.main != 'undefined' && weather.main.temp < 15 ? 'cold' : 'warm'">
    <div class="date-box">
      {{ dateBuilder() }}
    </div>
    <div class="search-box">
      <input 
        type="text" 
        class="search-bar" 
        placeholder="Please type a city.."
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>   
    
    <Transition>
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">            
        <div class="weather-additional">         
          <div class="cloud"><font-awesome-icon icon="cloud"/> {{ weather.clouds.all }}%</div>
          <div class="wind"><font-awesome-icon icon="wind"/> {{ weather.wind.speed }}m/s</div>        
          <div class="humidity"><font-awesome-icon icon="droplet"/> {{ weather.main.humidity }}%</div>
        </div>
        <div class="weather-temp">          
          <div class="temp">
            {{ Math.round(weather.main.temp) }}          
          </div>   
          <div class="temp_additional">
            <div class="temp_celcius">°C</div>
            <div class="temp_minmax">
              <div class="temp_max"><font-awesome-icon icon="arrow-down" />{{ Math.round(weather.main.temp_max) }}°</div>
              <div class="temp_min"><font-awesome-icon icon="arrow-up" />{{ Math.round(weather.main.temp_min) }}°</div>                                         
            </div>
          </div>     
        </div>   
        <div class="weather_sky">
          <font-awesome-icon v-if="weather_sky === 'Thunderstorm'" icon="cloud-bolt"/>
          <font-awesome-icon v-else-if="weather_sky === 'Drizzle'" icon="cloud-rain"/>
          <font-awesome-icon v-else-if="weather_sky === 'Rain'" icon="cloud-showers-heavy"/>
          <font-awesome-icon v-else-if="weather_sky === 'Snow'" icon="snowflake"/>
          <font-awesome-icon v-else-if="weather_sky === 'Mist' || 
                                        weather_sky === 'Haze' || 
                                        weather_sky === 'Fog'||
                                        weather_sky === 'Squall'" icon="smog"/>
          <font-awesome-icon v-else-if="weather_sky === 'Smoke' || 
                                        weather_sky === 'Dust' || 
                                        weather_sky === 'Sand' ||
                                        weather_sky === 'Ash' " icon="smog"/>
          <font-awesome-icon v-else-if="weather_sky === 'Tornado'" icon="tornado"/>
          <font-awesome-icon v-else-if="weather_sky === 'Clouds'" icon="cloud"/>
          <font-awesome-icon v-else icon="sun"/>
          <div class="weather">{{ weather_sky }}</div>
        </div>       
      </div>    
    </Transition>
  </div>  
</template>

<script>
  //https://openweathermap.org/api/one-call-api
  export default {
    name: 'app',
    data () {
      return {
        api_key: process.env.VUE_APP_API_KEY,
        url_base: 'https://api.openweathermap.org/data/2.5/',
        query: '',
        weather: {},
        weather_sky: ''
      }
    },
    mounted () {
      document.body.classList.add('warm');
      this.query = "Paris, FR";
      fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
      }).then(this.setResults);
    },
    methods: {
      fetchWeather (e) {
        if (e.key == "Enter") {
          fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
          }).then(this.setResults);
        }
      },
      setResults (results) {
        this.weather = results;
        this.query = this.weather.name+", "+this.weather.sys.country;
        this.weather_sky = this.weather.weather[0].main;
        if (this.weather.main != 'undefined' && this.weather.main.temp < 15) {
          document.body.classList.add("cold");
          document.body.classList.remove("warm");
        }
        else {
          document.body.classList.add("warm");
          document.body.classList.remove("cold");
        }
      },
      dateBuilder () {
        let d = new Date();
        let months = ["Jan.", "Feb.", "March", "April", "May", "June", "July", "Aug.", "Sept.", "Oct.", "Nov.", "Dec."];
        let days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
        let day = days[d.getDay()];
        let date = d.getDate();
        let month = months[d.getMonth()];
        let year = d.getFullYear();
        return `${day} ${date} ${month} ${year}`;
      }
    }
  }
</script>
