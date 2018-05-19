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
        <form>
          <div class="form group">
            <div class="m-3">
              <input type="text" class="form-control" placeholder="City">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="State">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="Zip">
            </div>
            <div class="m-3">
              <input type="text" class="form-control" placeholder="Country">
            </div>
            <div class="m-3">
              <button type="button" class="btn btn-outline-primary" style="width:100%">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import weather from '../../weather.js';

export default {
  name: 'weather',
  methods: {
    getWeather() {
      const query = process.argv
        .slice(2)
        .join('_')
        .replace(' ', '_');
      weather.get(query);
    }
  }
};
</script>


<style>
</style>
