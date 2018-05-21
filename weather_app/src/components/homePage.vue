<template>
  <div class="container">
    <div class="justify-content-center m-4">
      <div class="row justify-content-center">
        <img src="../assets/cloud.png" class="img-rounded img-fluid mh-100" style="height:300px" alt="Cartoon Cloud">
      </div>
      <div class="row justify-content-center m-3">
        <h1>Let's get that temp!</h1>
      </div>
      <div>
        <form v-on:submit.prevent="formatQuery">
          <div class="form group">
            <div class="m-3">
              <input type="text" class="form-control" placeholder="City" v-model="query.city">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="State" v-model="query.state">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="Zip" v-model="query.zip">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="Country" v-model="query.country">
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
      location: ''
    };
  },
  methods: {
    formatQuery() {
      console.log('in formatQuery func');
      if (this.query.zip) {
        this.location = this.query.zip;
      } else if (this.query.state && this.query.city) {
        this.location = `${this.query.state}/${this.query.city}`;
      } else if (this.query.country && this.query.city) {
        this.location = `${this.query.country}/${this.query.city}`;
      }
      this.getWeather();
    },
    printWeather() {
      console.log('in printWeather func');
    },
    getWeather() {
      console.log('in getWeather func');
      const request = https.get(
        `https://api.wunderground.com/api/${api.key}/conditions/q/${
          this.location
        }.json`,
        response => {
          console.log(request);
          this.printWeather();
        }
      );
    }
  }
};
</script>


<style>
</style>
