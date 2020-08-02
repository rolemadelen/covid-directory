<template>
  <div>
    <h2> {{ title }}</h2>

    <div id="wrapper">
      <div>
        <div v-if="country === null">
        </div>
        <div class="stats" v-else>
          <div>
            <img v-bind:src="country.flag">
          </div>
          <div class="sub-stats">
            <div class="general-stat">
              <span>Name</span>: {{ country.name }} <br>
              <span>Native Name</span>: {{ country.nativeName }} <br>
              <span>Capital</span>: {{ (country.capital === '') ? 'N/A' : country.capital }} <br>
              <span>Population</span>: {{ country.population.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }}  <br>
              <span> Region</span>: {{ (country.region === '') ? 'N/A' : country.region }} <br>
              <span> Sub-region</span>: {{ (country.subregion === '') ? 'N/A' : country.subregion }} <br>
              <span>Currency</span>: {{ (country.currencies)[0].name }} ({{(country.currencies)[0].code}}, {{ (country.currencies)[0].symbol }})
            </div>
            <div class="covid-stat">
              <span>Date</span>: {{ country.date }} <br>
              <span>Total Cases</span>: {{ country.total_cases.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
              <span>New Cases</span>: {{ country.new_cases.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
              <span>Total Deaths</span>: {{ country.total_deaths.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
              <span> New Deaths</span>: {{ country.new_deaths.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
              <span>Total Cases per Million</span>: {{ country.total_cases_per_million }} <br>
              <span>New Cases per Million</span>: {{ country.new_cases_per_million }} <br>
              <span>Total Deaths per Million</span>: {{ country.total_deaths_per_million }} <br>
              <span>New Deaths per Million</span>: {{ country.new_deaths_per_million }} <br>
            </div>
          </div>
        </div>
      </div>

      <hr style="clear: both;">

      <div class="countryList">
        <div v-for="r in region">
          <h2> {{ r }}</h2>
          <span v-for="c in countryCovidTable" v-if="c.region === r">
            <a href="#"><button @click="findCountry(c.name)"> {{c.name}} </button></a>
          </span>
        </div>
      </div>
    </div>
    <hr>

    <div class="ref">
      COVID data from <a href="https://ourworldindata.org/covid-deaths?country=~JPN">Our World in Data</a>.
    </div>

  </div>
</template>

<script>
import json from './json/countries';
import covid from './json/covid-data';

export default {
  created() {
    this.createCountryCovidTable();
    this.readData();
  },
  data() {
    return{
      title: "Countries-COVID Directiory",
      country: null,
      countries: json,
      covid: covid,
      region: [
      ],
      countryCovidTable: [
      ],
    }
  }, 
  methods: {
    createCountryCovidTable: function() {
      this.countries.forEach(c => {
        if(this.covid[c.alpha3Code] !== undefined) {
          this.countryCovidTable.push(Object.assign({}, c, this.covid[c.alpha3Code].data.pop()));
        }
      })
    },
    findCountry: function(name) {
      this.country = this.countryCovidTable.find(e => e.name === name)
    },
    readData: function() {
      this.countryCovidTable.forEach(e => {
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
  width: 650px;
  height: 450px;
}

hr {
  clear: both;
}

.stats {
}

.general-stat span {
  width: 100px;
}
.covid-stat span {
  width: 180px;
}
.general-stat span, .covid-stat span {
  font-weight: bold;
  margin: 5px 0 0 15px;
  display: inline-block;
}

#wrapper {
  display: flex;
  flex-direction: column;
}

#wrapper .countryList {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}

h2 {
  margin: 10px 0 0 10px;
  padding: 0;
}

.stats {
  display: flex;
  flex-direction: column;
}

.sub-stats {
  display: flex;
  flex-direction: row;
}

.ref {
  font-size: 13px;
  text-align: center;
}

/* https://www.csswand.dev/ */
button {
  color: #1c1c1c;
  background-color: #e4e4e4;
  border: 1px solid #969696;
  border-radius: 4px;
  padding: 0 10px;
  margin: 2px;
  cursor: pointer;
  font-size: 14px;
  outline: none;
  transition: all 0.2s ease-in-out;
}
button:hover {
  border-radius: 40%;
  border-color: #2299ff;
}
</style>
