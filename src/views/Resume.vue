<template>
  <div>
    <navbar v-bind:showMenu="false" />
    <mdb-container style="height: 100%">
          <section class="text-center">
            <h2 class="h1 pt-4">{{ $t('resume.title') }}</h2>

            <p class="text-primary font-weight-bold mb-5 mt-5 px-3">{{ $t('resume.desc') }}</p>
          </section>
      <mdb-row>
        <mdb-col xs="12" :sm="weatherChosenCity == flightsChosenCity ? '12' : '6'" class="mt-2">
            <mdb-card :style="chosenCity == weatherChosenCity ? 'box-shadow: 0 2px 5px 0 rgba(0,0,255,0.70), 0 2px 10px 0 rgba(0,0,255,0.70)' : ''">
              <mdb-card-image :src="require('@/assets/' + cities[weatherChosenCity].name.toLowerCase() + '.jpg')"></mdb-card-image>
              <mdb-card-body class="pb-2">
                <mdb-card-title>
                  {{ cities[weatherChosenCity].name }}
                </mdb-card-title>
                <mdb-card-text>
                  <h5 v-if="weatherChosenCity != flightsChosenCity">{{ $t('resume.chosen_for_weather') }}</h5>
                  <h5 v-if="weatherChosenCity == flightsChosenCity">{{ $t('resume.chosen_for_both') }}</h5>
                  <p class="font-weight-bold">{{ $t('resume.weather') }}</p>
                  <p class="text-primary font-weight-bold">{{ $t('resume.current_weather') }}</p>
                  <div v-if="cities[weatherChosenCity].forecast">
                    <img v-if="cities[weatherChosenCity].forecast" :src="cities[weatherChosenCity].forecast.iconUrl" />
                    <h5 class="mt-2">{{ (cities[weatherChosenCity].forecast || {}).text || '...' }}, <span class="font-weight-bold">{{ (cities[weatherChosenCity].forecast || {}).temperature || '... °C' }}</span></h5>
                  </div>
                  <div v-if="cities[weatherChosenCity].forecast == undefined">
                    <div v-if="!accuweatherError" class="spinner-border text-light mt-4" role="status">
                      <span class="sr-only">Loading...</span>
                    </div>
                    <p v-if="!accuweatherError" class="text-small text-grey font-italic">{{ $t('common.loading') }}</p>
                    <p v-if="accuweatherError" class="text-small text-grey font-italic">{{ $t('common.error_aw') }}</p>
                  </div>
                  <p class="font-weight-bold mt-4">{{ $t('resume.flights') }}</p>
                  <p class="text-primary font-weight-bold">{{ $t('resume.flights_cost') }}</p>
                  <mdb-row>
                    <mdb-col class="scrollbar scrollbar-primary">
                        <mdb-tbl sm>
                          <mdb-tbl-head>
                            <tr>
                              <th>{{ $t('flights.destination') }}</th>
                              <th>{{ $t('flights.price') }}</th>
                              <th>{{ $t('flights.time') }}</th>
                              <th>{{ $t('flights.duration') }}</th>
                            </tr>
                          </mdb-tbl-head>
                          <mdb-tbl-body v-for="flight in cities[weatherChosenCity].flights" :key="flight.to">
                            <tr>
                              <th class="font-weight-bold">{{ flight.to }}</th>
                              <td>{{ flight.price }} €</td>
                              <td>{{ flight.time }}</td>
                              <td>{{ flight.duration }}</td>
                            </tr>
                          </mdb-tbl-body>
                        </mdb-tbl>
                        <div class="spinner-border text-light mt-4" role="status" v-if="!(cities[weatherChosenCity].flights && cities[weatherChosenCity].flights.length > 0)">
                          <span class="sr-only">Loading...</span>
                        </div>
                    </mdb-col>
                  </mdb-row>
                </mdb-card-text>
                <mdb-btn color="primary" v-on:click="select(weatherChosenCity)" style="width: 100%">
                  <mdb-icon fas icon="check-square" v-if="chosenCity == weatherChosenCity"/>
                  <mdb-icon fas icon="stop" v-if="chosenCity != weatherChosenCity"/>
                  {{ $t('common.select') }}
                </mdb-btn>
              </mdb-card-body>
            </mdb-card>
        </mdb-col>

        <mdb-col xs="12" sm="6" class="mt-2" v-if="weatherChosenCity !== flightsChosenCity">
            <mdb-card :style="chosenCity == flightsChosenCity ? 'box-shadow: 0 2px 5px 0 rgba(0,0,255,0.70), 0 2px 10px 0 rgba(0,0,255,0.70)' : ''">
              <mdb-card-image :src="require('@/assets/' + cities[flightsChosenCity].name.toLowerCase() + '.jpg')"></mdb-card-image>
              <mdb-card-body class="pb-2">
                <mdb-card-title>
                  {{ cities[flightsChosenCity].name }}
                </mdb-card-title>
                <mdb-card-text>
                  <h5>{{ $t('resume.chosen_for_flights') }}</h5>
                  <p class="font-weight-bold">{{ $t('resume.weather') }}</p>
                  <p class="text-primary">{{ $t('resume.current_weather') }}</p>
                    <div v-if="cities[flightsChosenCity].forecast">
                    <img v-if="cities[flightsChosenCity].forecast" :src="cities[flightsChosenCity].forecast.iconUrl" />
                    <h5 class="mt-2">{{ (cities[flightsChosenCity].forecast || {}).text || '...' }}, <span class="font-weight-bold">{{ (cities[flightsChosenCity].forecast || {}).temperature || '... °C' }}</span></h5>
                  </div>
                  <div v-if="cities[flightsChosenCity].forecast == undefined">
                    <div v-if="!accuweatherError" class="spinner-border text-light mt-4" role="status">
                      <span class="sr-only">Loading...</span>
                    </div>
                    <p v-if="!accuweatherError" class="text-small text-grey font-italic">{{ $t('common.loading') }}</p>
                    <p v-if="accuweatherError" class="text-small text-grey font-italic">{{ $t('common.error_aw') }}</p>
                  </div>
                  <p class="font-weight-bold mt-4">{{ $t('resume.flights') }}</p>
                  <p class="text-primary">{{ $t('flights.flights_cost') }}</p>
                  <mdb-row>
                    <mdb-col class="scrollbar scrollbar-primary">
                        <mdb-tbl sm>
                          <mdb-tbl-head>
                            <tr>
                              <th>{{ $t('flights.destination') }}</th>
                              <th>{{ $t('flights.price') }}</th>
                              <th>{{ $t('flights.time') }}</th>
                              <th>{{ $t('flights.duration') }}</th>
                            </tr>
                          </mdb-tbl-head>
                          <mdb-tbl-body v-for="flight in cities[flightsChosenCity].flights" :key="flight.to">
                            <tr>
                              <th class="font-weight-bold">{{ flight.to }}</th>
                              <td>{{ flight.price }} €</td>
                              <td>{{ flight.time }}</td>
                              <td>{{ flight.duration }}</td>
                            </tr>
                          </mdb-tbl-body>
                        </mdb-tbl>
                        <div class="spinner-border text-light mt-4" role="status" v-if="!(cities[flightsChosenCity].flights && cities[flightsChosenCity].flights.length > 0)">
                          <span class="sr-only">Loading...</span>
                        </div>
                    </mdb-col>
                  </mdb-row>
                </mdb-card-text>
                <mdb-btn color="primary" v-on:click="select(flightsChosenCity)" style="width: 100%">
                  <mdb-icon fas icon="check-square" v-if="chosenCity == flightsChosenCity"/>
                  <mdb-icon fas icon="stop" v-if="chosenCity != flightsChosenCity"/>
                  {{ $t('common.select') }}
                </mdb-btn>
              </mdb-card-body>
            </mdb-card>
        </mdb-col>
      </mdb-row>
      <div class="mt-4 pb-4">
        <mdb-btn color="primary" v-on:click="goToNext" :disabled="chosenCity === undefined"><mdb-icon icon="arrow-right" class="mr-1"/> {{ $t('resume.next_step') }}</mdb-btn>
      </div>
    </mdb-container>
  </div>
</template>

<script lang="ts">

  import Vue from 'vue';

  import navbar from '@/components/navbar.vue';
  import { mdbCardImage, mdbCardText, mdbModal, mdbModalHeader, mdbModalTitle, mdbModalBody, mdbModalFooter, mdbContainer, mdbCard, mdbBtn, mdbRow, mdbCol, mdbCardUp, mdbCardAvatar, mdbCardBody, mdbCardTitle, mdbIcon, mdbInput, mdbTbl, mdbTblBody, mdbTblHead } from 'mdbvue';
  import router from '../router';

  import store from '../store';

  export default Vue.extend({
    name: 'Cities',
    data: function() {
      return {
        cities: store.state.cities, 
        chosenCity: undefined,
        weatherChosenCity: store.state.weatherChosenCity,
        flightsChosenCity: store.state.flightsChosenCity,
        accuweatherError: false
      }
    },
    methods: {
      goToNext: function() {
        store.commit('CHOOSE_FINAL', this.chosenCity);
        router.push('/result');
      },
      select: function(cityIndex) {
        this.chosenCity = cityIndex;
      }
    },
    mounted: function() {
        // 
        if ( this.weatherChosenCity == this.flightsChosenCity ) {
          this.chosenCity = this.weatherChosenCity;
          this.goToNext();
        }
        //
        Promise.all([
          this.$store.dispatch('initFlights'),
          this.$store.dispatch('initWeather')
        ]).then(() => {
          // console.log('Flights init');
        }).catch(err => {
          this.accuweatherError = true;
          console.log('ERROR', err);
        });
    },
    components: {
      mdbContainer,
      navbar,
      mdbCard, 
      mdbBtn,
      mdbRow,
      mdbCol,
      mdbCardUp, 
      mdbCardAvatar, 
      mdbCardBody, 
      mdbCardTitle, 
      mdbIcon,
      mdbInput,
      mdbModal, 
      mdbModalHeader, 
      mdbModalTitle,
      mdbModalBody, 
      mdbModalFooter,
      mdbCardImage,
      mdbCardText,
      mdbTbl,
      mdbTblBody,
      mdbTblHead
    },
  });

</script>

<style scoped>

  .scrollbar {
    float: left;
    height: 300px;
    width: 150px;
    background: #fff;
    overflow-y: scroll;
    margin-bottom: 25px;
  }

  .scrollbar-primary::-webkit-scrollbar {
    width: 12px;
    background-color: #F5F5F5;
  }

  .scrollbar-primary::-webkit-scrollbar-thumb {
    border-radius: 10px;
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
    background-color: #4285F4;
  }

</style>