<template>
  <div class="container">
    <div class="justify-content-center m-4">
      <div class="row justify-content-center">
        <img src="../assets/cloud.png" class="img-rounded img-fluid mh-100" style="height:300px" alt="Cartoon Cloud">
      </div>
      <div v-if="!forecastReceived" class="row justify-content-center m-3 !forecastReceived">
        <h1>Let's get that temp!</h1>
      </div>
      <div v-if="forecastReceived" class="row justify-content-center m-3 forecastReceived">
        <h1>Here's your forecast!</h1>
      </div>
      <div class="row justify-content-center m-3">
        <img v-if="currentForecast" :src="currentIcon" alt="">
        <h3 v-if="currentForecast">{{ currentForecast }}</h3>
      </div>
      <div>
        <form v-on:submit.prevent="getWeather">
          <div class="form group">
            <div class="m-3">
              <input type="text" class="form-control" placeholder="City" v-model="query.city">
            </div>
            <div class="m-3">
              <b-form-select v-model="query.state" :options="options" class="form-control" title="State"></b-form-select>
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="Zip" v-model="query.zip">
            </div>
            <div class="m-3">
              <button type="submit" class="btn btn-outline-primary" style="width:100%">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import weather from '../../weather.js';
import api from '../../api/api.json';
import https from 'https-browserify';

export default {
  name: 'weather',
  data() {
    return {
      query: {},
      currentForecast: '',
      tenDayForecast: [],
      currentIcon: '',
      forecastReceived: false,
      options: [
        { title: null, text: 'State' },
        { value: 'AL', text: 'Alabama' },
        { value: 'AS', text: 'Alaska' },
        { value: 'AZ', text: 'Arizona' },
        { value: 'AK', text: 'Arkansas' },
        { value: 'CA', text: 'California' },
        { value: 'CO', text: 'Colorado' },
        { value: 'CT', text: 'Connecticut' },
        { value: 'DE', text: 'Delaware' },
        { value: 'FL', text: 'Florida' },
        { value: 'GA', text: 'Georgia' },
        { value: 'HI', text: 'Hawaii' },
        { value: 'ID', text: 'Idaho' },
        { value: 'IL', text: 'Illinois' },
        { value: 'IN', text: 'Indiana' },
        { value: 'IA', text: 'Iowa' },
        { value: 'KA', text: 'Kansas' },
        { value: 'KY', text: 'Kentucky' },
        { value: 'LA', text: 'Louisiana' },
        { value: 'ME', text: 'Maine' },
        { value: 'MY', text: 'Maryland' },
        { value: 'MA', text: 'Massachusettes' },
        { value: 'MI', text: 'Michigan' },
        { value: 'MN', text: 'Minnessota' },
        { value: 'MS', text: 'Mississippi' },
        { value: 'MO', text: 'Missouri' },
        { value: 'MT', text: 'Montana' },
        { value: 'NE', text: 'Nebraska' },
        { value: 'NV', text: 'Nevada' },
        { value: 'NH', text: 'New Hampshire' },
        { value: 'NJ', text: 'New Jersey' },
        { value: 'NM', text: 'New Mexico' },
        { value: 'NY', text: 'New York' },
        { value: 'NC', text: 'North Carolina' },
        { value: 'ND', text: 'North Dakota' },
        { value: 'OH', text: 'Ohion' },
        { value: 'OK', text: 'Oklahoma' },
        { value: 'OR', text: 'Oregon' },
        { value: 'PA', text: 'Pennsylvania' },
        { value: 'RI', text: 'Rhode Island' },
        { value: 'SC', text: 'South Carolina' },
        { value: 'SD', text: 'South Dakota' },
        { value: 'TN', text: 'Tennessee' },
        { value: 'TX', text: 'Texas' },
        { value: 'UT', text: 'Utah' },
        { value: 'VT', text: 'Vermont' },
        { value: 'VI', text: 'Virginia' },
        { value: 'WA', text: 'Washington' },
        { value: 'DC', text: 'Washington D.C.' },
        { value: 'WV', text: 'West Virginia' },
        { value: 'WI', text: 'Wisconsin' },
        { value: 'WY', text: 'Wyoming' }
      ]
    };
  },
  computed: {
    placeForWeather: function() {
      console.log('in placeForWeather func', this.query);
      const rawCity = this.query.city;
      this.query.city = rawCity.split(' ').join('_');
      let placeForWeather = '';
      if (this.query.zip) {
        placeForWeather = this.query.zip;
      } else if (this.query.state && this.query.city) {
        placeForWeather = `${this.query.state}/${this.query.city}`;
      } else if (this.query.country && this.query.city) {
        placeForWeather = `${this.query.country}/${this.query.city}`;
      }
      return placeForWeather;
    }
  },
  methods: {
    getWeather() {
      try {
        const request = https.get(
          `https://api.wunderground.com/api/${
            api.key
          }/geolookup/conditions/forecast10day/q/${this.placeForWeather}.json`,
          response => {
            if (response.statusCode === 200) {
              let body = '';
              // read the data
              response.on('data', chunk => {
                body += chunk;
              });
              response.on('end', () => {
                try {
                  const weather = JSON.parse(body);
                  // print data
                  if (weather.location) {
                    this.roundWeather(weather);
                    this.$data.query = {};
                  } else {
                    const queryError = new Error(
                      `The location "${this.placeForWeather}" was not found.`
                    );
                    this.printError(queryError);
                  }
                } catch (error) {
                  // parse error
                  this.printError(error);
                }
              });
            } else {
              // status code error
              const statusCodeError = new Error(
                `There was an error getting the message for ${readableQuery}. (${
                  http.STATUS_CODES[response.statusCode]
                })`
              );
              this.printError(statusCodeError);
            }
          }
        );
        request.on('error', this.printError);
      } catch (error) {
        // malformed URL error
        this.printError(error);
      }
    },
    // print out error message
    printError(error) {
      console.log(error.message);
    },
    // print out temp details
    printWeather(weather, roundedTempF, roundedTempC) {
      this.forecastReceived = true;
      const currentTempMessage = `The current temperature in ${
        weather.location.city
      } is ${roundedTempF}f and ${roundedTempC}c`;
      this.currentForecast = currentTempMessage;
      const forecastMessage = `Forecast in ${weather.location.city} is ${
        weather.forecast.simpleforecast.forecastday
      }`;
    },
    roundWeather(weather) {
      const roundedTempF = Math.round(weather.current_observation.temp_f);
      const roundedTempC = Math.round(weather.current_observation.temp_c);
      this.printWeather(weather, roundedTempF, roundedTempC);
      this.getIcon(weather);
    },
    getIcon(weather) {
      this.currentIcon = weather.current_observation.icon_url;
    }
  }
};
</script>


<style>
.forecastReceived {
  visibility: hidden;
}
</style>
