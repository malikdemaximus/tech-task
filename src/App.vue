<template>
  <div id="app" class="container flex app-content">
    <div class="filters flex">
      <Filter
        v-model="checkedOptions"
        :title="'Опции тарифа'"
        :options="optionsFilterData"
        ref="filterOption"
      />
      <Filter
        v-model="checkedAirlines"
        :title="'Авиакомпании'"
        :options="flightsData.airlines"
        ref="filterAirline"
      />
      <span class="reset-filters" @click="resetAll">Сбросить все фильтры</span>
    </div>
    <div>
      <flight-card :key="flight.id" :flight="flight" v-for="flight in flights" />
    </div>
  </div>
</template>

<script>
import Filter from './components/Filter.vue'
import FlightCard from './components/FlightCard.vue'
import data from './data/data'


export default {
  name: 'App',
  components: {
    FlightCard,
    Filter
  },
  data() {
    return {
      flightsData: data,
      checkedAirlines: [],
      checkedOptions: [],
      flights: data.flights,
      optionsFilterData: {
        straight: 'Только прямые',
        luggage: 'Только с багажом',
        refundable: 'Только возвратные',
      }
    }
  },
  watch: {
    checkedAirlines(arr) {
      this.flights = data.flights
      if (arr.length > 0) {
        let filteredData = []
        arr.forEach((value) => {
          filteredData = [...filteredData, ...this.flights.filter(d => d.validating_carrier === value)]
        })
        this.flights = filteredData
      }
    },
    checkedOptions(arr) {
      this.flights = data.flights
      if (arr.length > 0) {
        arr.forEach((value) => {
          switch (value) {
            case 'refundable':
              this.flights = this.flights.filter(d => d.itineraries[0][0].refundable === true)
              break
            case 'luggage':
              this.flights = this.flights.filter(d => d.itineraries[0][0].segments[0].baggage_options[0].value !== 0)
              break
            case 'straight':
              this.flights = this.flights.filter(d => d.itineraries[0][0].stops === 0)
              break
          }
        })
      }
    }
  },
  methods: {
    resetAll() {
      this.$refs['filterAirline'].resetFilter()
      this.$refs['filterOption'].resetFilter()
    }
  }
}
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600;700&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Open Sans", sans-serif;
}

body {
  color: #202123;
  background-color: #e1e1e1;
  transition: 0.4s;
}

.container {
  margin: 0 auto;
  max-width: 1140px;
}

a {
  text-decoration: none;
}

button {
  cursor: pointer;
  border: none;
  outline: none;
}

img {
  max-width: 100%;
  height: auto;
}

.flex {
  display: flex;
}

.app-content {
  gap: 20px;
  padding-top: 50px;
}

/* Filters */

.filters {
  width: 240px;
  flex-direction: column;
  gap: 12px;
}

.reset-filters {
  font-size: 12px;
  line-height: 16px;
  color: #7284e4;
  border-bottom: 1px dashed #aab3e3;
  cursor: pointer;
  width: fit-content;
}

.filter-box {
  background: #f5f5f5;
  border-radius: 4px;
  padding-bottom: 12px;
}

.filter-header {
  padding: 12px;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 4px;

  h3 {
    font-size: 14px;
    line-height: 20px;
    font-weight: 700;
  }
}

/* Checkbox */
.checkbox-item {
  padding: 0 0 14px 12px;
}
.checkbox-item {
  display: block;
  position: relative;
  padding: 10px 0 10px 36px;
  cursor: pointer;
  font-size: 12px;
  line-height: 12px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;

  input {
    position: absolute;
    opacity: 0;
    cursor: pointer;
    height: 0;
    width: 0;

    &:checked ~ .checkmark {
      background-color: #55bb06;
      border: 1px solid #55bb06;
    }
  }

  &:hover {
    background-color: #ebebeb;

    input ~ .checkmark {
      background-color: #fff;
      border: 1px solid #b9b9b9;

      &:after {
        display: block;
        border: solid #b9b9b9;
        border-width: 0 2px 2px 0;
      }
    }
  }
}

.checkmark {
  position: absolute;
  top: 10px;
  left: 12px;
  height: 12px;
  width: 12px;
  border-radius: 2px;
  background-color: #fff;
  border: 1px solid #b9b9b9;

  &:after {
    content: "";
    position: absolute;
    display: none;
  }
}

.checkbox-item input:checked ~ .checkmark:after {
  display: block;
}

.checkbox-item .checkmark:after {
  left: 2.5px;
  top: -0.5px;
  width: 3px;
  height: 6px;
  border: solid white;
  border-width: 0 2px 2px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

/* Tooltip */

.close {
  cursor: pointer;
  position: relative;
  .tooltiptext {
    width: 122px;
    visibility: hidden;
    font-size: 12px;
    line-height: 12px;
    background-color: #555;
    color: #202123;
    text-align: center;
    border-radius: 6px;
    padding: 12px 0;
    position: absolute;
    z-index: 1;
    bottom: 125%;
    left: 50%;
    margin-left: -60px;
    opacity: 0;
    transition: opacity 0.3s;
    background: #ffffff;
    border: 1px solid #e1e1e1;
    filter: drop-shadow(0px 2px 3px rgba(0, 0, 0, 0.05))
      drop-shadow(0px 3px 6px rgba(0, 0, 0, 0.1));

    &::after {
      content: "";
      position: absolute;
      top: 100%;
      left: 50%;
      margin-left: -5px;
      border-width: 5px;
      border-style: solid;
      border-color: #ffffff transparent transparent transparent;
    }
  }
  &:hover .tooltiptext {
    visibility: visible;
    opacity: 1;
  }
}

@media only screen and (max-width: 767px) {
  .app-content {
    flex-direction: column;
    padding-left: 15px;
    padding-right: 15px;
  }

  .filters {
    width: 100%;
  }
}
</style>
