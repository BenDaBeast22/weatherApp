<template>
  <div id="app" :class="time">
    <main>
      <div class="search-box">
        <input type="text"
        class="search-bar"
        placeholder="Search..."
        v-model="query"
        @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main !== 'undefined' ">
        <div class="location-box">
          <div class="location">{{weather.name}}, {{weather.sys.country}}</div>
          <div class="date">{{date}}</div>
        </div>

        <div class="weather-box">
          <div class="temperature">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>

      <div class="misaka-mikoto">
        <img v-if="initialQueryDone && mood ==='happy'" src="./assets/misaka-happy.png" @click="changeMikoto" class="happy">
        <img v-if="initialQueryDone && mood === 'not-impressed'" src="./assets/misaka-not-impressed.png" @click="changeMikoto" class="not-impressed">
      </div>

      <div class="change-time">
        <img src="./assets/time.png" alt="change time" class="time" @click="changeTime">
      </div>
    </main>

  </div>
</template>



<script>
export default {
  name: 'App',
  data (){
    return {
      api_key: '3c96be5f6bdb5faa7e3e6c59c9586fe8',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      date: '',
      mood: 'happy',
      moodArr: ["happy", "not-impressed"],
      misakaSrc: './assets/misaka-happy.png',
      time: 'day',
      initialQueryDone: false
    }
  },
  methods: {
    fetchWeather(e) {
      if(e.key === "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults(results){
      this.weather = results;
      if(this.weather.main.temp > 0){
        this.mood = "happy";
        this.misakaSrc = "./assets/misaka-happy.png"
      } else if(this.weather.main.temp <= 0){
        this.mood = "not-impressed";
        this.misakaSrc = "./assets/misaka-happy.png";
      }
      this.initialQueryDone = true;
    },
    changeMikoto(){
      const moodArr = this.moodArr;
      let index = moodArr.indexOf(this.mood);
      if(index < moodArr.length - 1){
        this.mood = moodArr[index + 1];
      } else {
        this.mood = moodArr[0];
      }
      // if(this.mood === "happy"){
      //   this.mood = "not-impressed";
      // } else if(this.mood === "not-impressed"){
      //   this.mood = "happy";
      // }
    },
    changeTime(){
      const timeArr = ["midnight", "morning", "day", "night"];
      let index = timeArr.indexOf(this.time);
      if(index < timeArr.length - 1){
        this.time = timeArr[index + 1];
      } else {
        this.time = timeArr[0];
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = ['January', 'February','March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let hours = d.getHours();

      // 11pm - 2:59am
      if(hours < 3 || hours > 22){
        this.time = "midnight";
      }
      // 3:00am - 9:59am
      else if(hours < 10){
        this.time = "morning";
      }
      // 8pm - 10:59pm
      else if(hours > 19 && hours < 23) {
        this.time = "night"
      }

      let date = d.getDate();
      let day = days[d.getDay()];
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      this.date = `${day}, ${month} ${date}, ${year}`;
    },
  },
  created () {
    this.query = "Toronto";
    this.fetchWeather({key: "Enter"});
    this.dateBuilder();
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
  font-family: 'montserrat', 'sans-serif';
}

#app {
  background-image: url("./assets/day.png");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.morning {
  background-image: url("./assets/morning.jpg");
}

#app.night {
  background-image: url("./assets/night.png");
}

#app.midnight {
  background-image: url("./assets/midnight.png")
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
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

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px 0 16px 0;
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: white;
  font-size: 20px;
  font-style: italic;
  font-weight: 300;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.weather-box {
  text-align: center;
}

.weather-box .temperature {
  color: white;
  display: inline-block;
  font-size: 102px;
  font-weight: 900;
  padding: 10px 25px;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: white;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.misaka-mikoto {
  animation: fadein 0.4s;
  position: absolute;
  margin-left: auto;
  margin-right: auto;
  left: 0;
  right: 0;
  bottom: 0;
  width: 40%;
  height: 380px;
  width: 500px;
  transition: 0.4s;
}

.misaka-mikoto:hover {
  opacity: 0.9;
}

.misaka-mikoto .not-impressed {
  position: absolute;
  bottom: 0;
  display: block;
  margin-left: auto;
  margin-right: auto;
  object-fit: cover;

  height: 100%;
  width: 100%;
}

.misaka-mikoto .happy {
  position: absolute;
  bottom: 0;
  display: block;
  margin-left: auto;
  margin-right: auto;
  object-fit: scale-down;

  height: 100%;
  width: 100%;
}
.change-time {
  position: absolute;
  bottom: 5%;
  right: 5%;
  width: 90px;
}
.change-time:hover {
  opacity: 0.8;
}
.time{
  width: 100%;
}

@keyframes fadein {
  0%    { opacity: 0; }
  10%   { opacity: 0.1 }
  20%   { opacity: 0.2 }
  30%   { opacity: 0.3 }
  40%   { opacity: 0.4 }
  50%   { opacity: 0.5 }
  60%   { opacity: 0.6; }
  70%   { opacity: 0.7; }
  80%   { opacity: 0.8; }
  90%   { opacity: 0.9 }
  100%  { opacity: 1; }
}

</style>
