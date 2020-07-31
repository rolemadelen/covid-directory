<template>
  <div>
    <h2> {{ title }}</h2>

    <div class="countryInfo">
      <div v-if="country === null">
        {{ readData() }}
      </div>
      <div v-else>
        <img v-bind:src="country.flag"><br>
        <span>Name</span>: {{ country.name }} <br>
        <span>Native Name</span>: {{ country.nativeName }} <br>
        <span>Capital</span>: {{ country.capital }} <br>
        <span>Population</span>: {{ country.population.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }}  <br>
        <span> Region</span>: {{ country.region }} <br>
        <span> Sub-region</span>: {{ country.subregion }} <br>
        <span>Currency</span>: {{ (country.currencies)[0].name }} ({{(country.currencies)[0].code}}, {{ (country.currencies)[0].symbol }})
      </div>
    </div>

    <hr>

    <div v-for="r in region">
      <h3 v-if="r===''">Unknown</h3>
      <h3 v-else> {{ r }}</h3>
      <span v-for="c in countries" v-if="c.region === r">
        <a href="#"><button @click="findCountry(c.name)"> {{c.name}} </button></a>
      </span>
    </div>
  </div>
</template>

<script>
import json from './json/countries';

export default {
  data() {
    return{
      title: "Countries Directiory",
      country: null,
      countries: json,
      region: [
      ],
    }
  }, 
  methods: {
    findCountry: function(name) {
      this.country = this.countries.find(e => e.name === name)
    },
    readData: function() {
      this.countries.forEach(e => {
        //alert(this.region.find(r => (r === e.region)) === undefined);
        if(this.region.includes(e.region)){ 

        } else { 
          this.region.push(e.region);
        }
      })
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Baloo+2:wght@500&display=swap');

* {
  font-family: 'Baloo 2', cursive;
}

body {
  background-color: #dddddd;
}

img {
  width: 380px;
  height: 250px;
}

div.countryInfo span {
  font-weight: bold;
  display: inline-block;
  width: 100px;
}

/* https://www.csswand.dev/ */
button {
  color: #1c1c1c;
  background-color: #e4e4e4;
  border: 1px solid #969696;
  border-radius: 4px;
  padding: 0 15px;
  margin: 3px;
  cursor: pointer;
  height: 32px;
  font-size: 14px;
  outline: none;
  transition: all 0.2s ease-in-out;
}
button:hover {
  border-radius: 40%;
  border-color: #2299ff;
}
</style>
