<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.name ? weather.name : ''
    "
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
        <button class="clear-input" v-if="clearButton" @click="clearSearchBar">
          x
        </button>
        <button class="search-button" @click="fetchWeather">Search</button>
      </div>
      <div class="error-block" v-if="errorBlock">
        {{ errorMessage }}
      </div>
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      api_key: '687d9719f5f8283fdddc12b78512d389',
      url_base: 'http://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      clearButton: false,
      errorBlock: false,
      errorMessage: '',
    };
  },
  methods: {
    fetchWeather(e) {
      if (e) {
        this.clearButton = true;
      }
      if (e.key === 'Enter' || e.type === 'click') {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((r) => {
            return r.json();
          })
          .then(this.setResult);
      }
    },
    clearSearchBar() {
      this.query = '';
      this.clearButton = false;
    },
    setResult(results) {
      if (results.cod === '404') {
        this.errorBlock = true;
        this.errorMessage = results.message;
      } else if (results.cod === '400') {
        this.errorBlock = true;
        this.errorMessage = results.message;
      } else {
        this.errorBlock = false;
        this.errorMessage = '';
      }
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December',
      ];
      let days = [
        'Sunday',
        'Monday',
        'Tuesday',
        'Wednesday',
        'Thursday',
        'Friday',
        'Saturday',
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
  },
  created() {
    // console.log('create', navigator);
  },
};
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
  background-image: url('./assets/cold-bg.jpeg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.warm {
  background-image: url('./assets/warm-bg.jpeg');
}
#app.Moscow {
  background-image: url('./assets/Moscow.jpeg');
}
#app.London {
  background-image: url('./assets/London.jpeg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
  position: relative;
  display: flex;
  justify-content: space-between;
}
.search-box .search-bar {
  display: block;
  width: 80%;
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
.search-button {
  border: none;
  outline: none;
  font-size: 20px;
  font-weight: 500;
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
  color: #fff;
  background: #3d618d;
  padding: 0 20px;
}
.search-button:hover {
  cursor: pointer;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px 0px 16px 0px;
}
.clear-input {
  color: #313131;
  font-size: 20px;
  font-weight: 500;
  background: transparent;
  border: none;
  position: absolute;
  right: 22%;
  top: 5px;
  opacity: 0.5;
}
.clear-input:hover {
  cursor: pointer;
}
.error-block {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
  color: #fff;
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
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
@media screen and (max-width: 540px) {
  .search-box {
    flex-direction: column;
  }
  .search-box .search-bar {
    width: 100%;
    margin-bottom: 20px;
  }
  .search-button {
    padding: 10px;
  }
}
</style>
