<template>
  <div>
    <h2>
      Countries-COVID Directiory
    </h2>

    <div id="view">
      <div id="wrapper" class="left">
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
                <span>Date</span>: 
                <input type="date" v-model:value="today" @change="findDataByDate"><br>
                <span>Total Cases</span>: {{ date.total_cases.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
                <span>New Cases</span>: {{ date.new_cases.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
                <span>Total Deaths</span>: {{ date.total_deaths.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
                <span> New Deaths</span>: {{ date.new_deaths.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }} <br>
                <span>Total Cases per Million</span>: {{ date.total_cases_per_million }} <br>
                <span>New Cases per Million</span>: {{ date.new_cases_per_million }} <br>
                <span>Total Deaths per Million</span>: {{ date.total_deaths_per_million }} <br>
                <span>New Deaths per Million</span>: {{ date.new_deaths_per_million }} <br>
              </div>
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

      <div class="right">
        Sort by Total ...
        <div class="sort-buttons">
          <label for="totalDeaths">
            Deaths 
          </label>
          <input type="radio" name="sort-type" value="totalDeaths" @click="sortCountries('deaths')">

          <label for="totalCases">
            Cases 
          </label>
          <input type="radio" name="sort-type" value="totalCases" @click="sortCountries('cases')"> <br>

          <label for="totalCasesPm">
            Cases/M
          </label>
          <input type="radio" name="sort-type" value="totalCasesPm" @click="sortCountries('casesPm')"> 

          <label for="totalDeathsPm">
            Deaths/M
          </label>
          <input type="radio" name="sort-type" value="totalDeathsPm" @click="sortCountries('deathsPm')"> <br>
        </div>
        <div class="sorted-countries">
          <ol>
            <li v-for="c in sortedCountry">
              {{c.country}} - {{c.val}}
            </li>
          </ol>
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
      // JSON files
      countries: json,
      covid: covid,

      // Data Joint tables
      countryCovidTable: [
      ],
      region: [
      ],

      // Data
      country: null,
      date: '',
      today: '',

      // Sort table
      sortedCountry: null,
      deaths: [],
      cases: [],
      casesPm: [],
      deathsPm: [],
    }
  }, 
  methods: {
    sortCountries: function(sortBy) {
      if (sortBy === 'deaths') {
        this.sortedCountry = this.deaths.sort((a, b) => {
          return b.val - a.val;
        });
      } else if (sortBy === 'cases') {
        this.sortedCountry = this.cases.sort((a, b) => {
          return b.val - a.val;
        });
      } else if (sortBy === 'casesPm') {
        this.sortedCountry = this.casesPm.sort((a, b) => {
          return b.val - a.val;
        });
      } else if (sortBy === 'deathsPm') {
        this.sortedCountry = this.deathsPm.sort((a, b) => {
          return b.val - a.val;
        });
      }
    },
    findDataByDate: function() {
      this.date = this.country.date.find(e => e.date === this.today)
    },
    createCountryCovidTable: function() {
      this.countries.forEach(c => {
        /////////////////////////////////////
        // @todo: Hong Kong is missing several data
        /////////////////////////////////////
        if(this.covid[c.alpha3Code] !== undefined) {
          let t = Object.assign({}, c, {date: this.covid[c.alpha3Code].data});
          this.countryCovidTable.push(t);

          this.deaths.push({
            'country': t.name,
            'val': t.date[t.date.length-1].total_deaths
          })
          if(this.deaths[this.deaths.length-1].val === undefined) {
            this.deaths.pop()
          }

          this.cases.push({
            'country': t.name,
            'val': t.date[t.date.length-1].total_cases
          })
          if(this.cases[this.cases.length-1].val === undefined) {
            this.cases.pop();
          }

          this.casesPm.push({
            'country': t.name,
            'val': t.date[t.date.length-1].total_cases_per_million
          })
          if(this.casesPm[this.cases.length-1].val === undefined) {
            this.casesPm.pop();
          }

          this.deathsPm.push({
            'country': t.name,
            'val': t.date[t.date.length-1].total_deaths_per_million
          })
          if(this.deathsPm[this.cases.length-1].val === undefined) {
            this.deathsPm.pop();
          }
        }
      })
    },
    findCountry: function(name) {
      this.country = this.countryCovidTable.find(e => e.name === name);
      this.date = this.country.date[this.country.date.length-1];
      this.today = this.date.date;
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
  width: 500px;
  height: 380px;
}

hr {
  clear: both;
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
  justify-content: flex-start;
  width: 800px;
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

#view { 
  display: flex;
  flex-direction: row;
  border: 1px solid pink;
}

.left {
}

.right {
  width: 2000px;
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
