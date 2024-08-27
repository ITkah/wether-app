<script>
import axios from 'axios';

export default {
  data () {
    return {
      city: "",
      error: "",
      info: null
    }
  },
  watch: {
    city(newValue) {
      if (newValue === "") {
        this.info = null; 
        this.error = "";
      }
    }
  },
  computed: {
    cityName() {
      return "(" + this.city  + ")"
    },
    showCity() {
      return "City: " + this.info.name
    },
    showTemp() {
      return "Temperature: " + this.info.main.temp + "°"
    },
    showTempMin() {
      return "Temperature min: " + this.info.main.temp_min + "°"
    },
    showTempMax() {
      return "Temperature max: " + this.info.main.temp_max + "°"
    },
    showFeels() {
      return "Temperature feels like: " + this.info.main.feels_like + "°"
    },
    showDes() {
      return "Wether type: " + this.info.weather[0].description
    },
  },
  methods: {
    getWether() {
      if(this.city.trim().length < 2) {
        this.error = "Please enter more than one character."
        return false;
      }
      this.error = "";
      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=2e3cf3832cddc20e542c9032e9b41c06`)
        .then(res => {
          this.info = res.data; 
          this.error = ""; 
        })
        .catch(err => {
          if (err.response && err.response.status === 404) {
            this.error = "City not found. Try another name.";
          } else {
            this.error = "Failed to retrieve data. Please try again.";
          }
          this.info = null;
        });
    },
    validateInput(event) {
      const regex = /^[A-Za-z\s]*$/;
      if (!regex.test(event.target.value)) {
        event.target.value = event.target.value.replace(/[^A-Za-z\s]/g, '');
      }
    }
  }
}
</script>

<template>
<div class="wether">
    <h1 class="wether__title">Weather app</h1>
    <p class="wether__text">Check the weather (enter only english letters)</p>
    <div class="wether__box">
      <input type="text" v-model="city" @input="validateInput" placeholder="Enter city">
      <button class="wether__btn" v-if="city != ''" @click="getWether()">Get weather</button>
      <button class="wether__disabled" v-else>Enter the city name</button>
    </div>
    <p class="wether__error">{{ error }}</p>
    <div class="wether__output" v-if="info != null">
      <p class="wether__output-text">{{ showCity }}</p>
      <p class="wether__output-text">{{ showTemp }}</p>
      <p class="wether__output-text">{{ showTempMin }}</p>
      <p class="wether__output-text">{{ showTempMax }}</p>
      <p class="wether__output-text">{{ showFeels }}</p>
      <p class="wether__output-text">{{ showDes }}</p>
    </div>
  </div>
</template>

<style scoped>
.wether {
  width: 80vw;
  height: 60vh;
  border-radius: 50px;
  background: #1f0f24;
  color: #fff;
  padding: 35px;
  max-width: 100%;
  box-sizing: border-box;
}
@media (max-width: 990px) {
  .wether {
    width: 90vw;
    height: 50vh;
  }
}
.wether__title {
  margin-bottom: 30px;
  text-align: center;
  font-size: 27px;
  font-weight: bold;
  line-height: 1.2;
} 
.wether__text {
  margin-bottom: 25px;
  font-size: 16px;
  font-weight: 400;
  text-align: center;
  line-height: 1;
  color: rgb(154, 151, 151);
}
.wether input {
  margin-right: 20px;
  background: transparent;
  border: 0;
  outline: none;
  box-shadow: none;
  border-bottom: 2px solid #110813;
  color: #fcfcfc;
  font-size: 14px;
  width: 100%;
  display: block;
  max-width: 250px;
  padding: 10px;
  border-radius: none;
}
.wether input:focus {
  border-bottom-color: #6e2d7d;
}
.wether__btn {
  background: #e3bc4d;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  border-radius: 10px;
  border: 2px solid #b99935;
  padding: 10px 15px;
  cursor: pointer;
  outline: none;
  box-shadow: none;
  white-space: nowrap;
  transition: transform 500ms ease;
}
.wether__disabled {
  background: #e3bc4d;
  color: #fff;
  display: flex;
  white-space: nowrap;
  align-items: center;
  justify-content: center;
  text-align: center;
  border-radius: 10px;
  border: 2px solid #b99935;
  padding: 10px 15px;
  cursor: pointer;
  outline: none;
  box-shadow: none;
  opacity: .6;
  pointer-events: none;
}
.wether__btn:hover {
  transform: scale(1.1) translateY(-5px);
}
.wether__box {
  display: flex;
  align-items: center;
  justify-content: center;
}
.wether__error {
  margin-top: 20px;
  text-align: center;
  color: #d03933;
  font-size: 16px;
  font-weight: 400;
}

.wether__output {
  max-width: 390px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1fr;
  grid-gap: 10px;
}
</style>