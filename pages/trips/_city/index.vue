<!-- eslint-disable vue/html-self-closing -->
<!-- eslint-disable vue/no-v-html -->
<!-- eslint-disable indent -->
<template>
  <div>
    <v-snackbar v-model="snackbar" top :timeout="5000" :color="color">
      <v-row>
        {{ text }}
        <v-spacer />
        <v-icon small color="white" class="ml-3" @click="alert = false">
          mdi-close
        </v-icon>
      </v-row>
    </v-snackbar>
    <v-expand-transition>
      <div class="t-container">
        <cities-slider v-show="showSearchForm" module="tripsResults" @getTrips="getTrips" />
      </div>
    </v-expand-transition>
    <v-img v-if="$fetchState.pendeing" max-width="200" class="blink-2 mx-auto" src="~/assets/images/tanfer.png" />
    <div v-else class="gray-wrapper">
      <div v-if="!$fetchState.error">
        <!-- packages fetched successfuly -->
        <div v-if="tripsResults.length && !$fetchState.pendeing" class="t-container">
          <div class="flight-head">
            <div class="flight-title">
              <div class="d-flex-middle-fh">
                <v-img v-if="tripsCity.cityImage" class="rounded-lg mr-3" max-width="100" :alt="tripsCity.cityImageAlt" :src="tripsCity.cityImage">
                  <span class="d-none">{{ tripsCity.cityImageCaption }}</span>
                </v-img>
                <div>
                  <h1 class="font-weight-medium">
                    {{ tripsCity.CityName }} trips
                  </h1>
                  <p v-if="tripsCity.CityDescription !== 'null'" class="body-2">
                    {{ tripsCity.CityDescription }}
                  </p>
                  <p v-else>
                    Description not availabe
                  </p>
                </div>
              </div>
            </div>
            <div data-w-id="19892d4d-59f2-17f4-e9b5-c0b1699f6aa6" class="serach-close" style="visibility: hidden;" @click="showSearchForm = !showSearchForm">
              Modify Search
            </div>
          </div>
          <div class="filter-open" @click="showFilters">
            <!-- <span>&lt; Filters</span> -->
          </div>
          <div class="flight-serch-result">
            <!-- <aside id="aside-filter-trip" data-w-id="1b2006bd-cf9d-42e1-d991-e4049c7b4038" class="aside-filter">
              <div class="aside-inner">
                <div id="close-filter" class="close-filter" @click="closeFilter">
                  <img src="images/close-icon.png" loading="lazy" alt="">
                </div>
                <div class="filfer-block">
                  <div class="aside-title-hed">
                    Filter your results
                  </div>
                  <div class="light-txt">
                    {{ tripsCount }} results found<br>
                  </div>
                </div>
                <div v-if="min && max" class="w-form">
                  <div class="filfer-block">
                    <div class="aside-title">
                      price
                    </div>
                    <v-row justify="space-between">
                      <v-subheader color="secondary">
                        ${{ min }}
                      </v-subheader>
                      <v-subheader color="secondary">
                        ${{ max }}
                      </v-subheader>
                    </v-row>
                    <v-range-slider
                      v-model="prices"
                      :min="min"
                      :max="max"
                      color="pink"
                      class="align-center"
                      thumb-label
                      height="40"
                      @change="filteate"
                    />
                  </div>
                </div>
              </div>
            </aside> -->
            <div class="results-block">
              <!-- <div v-for="trip in trips" :key="trip.id" class="cruise-result-block pa-5">
                <v-row v-if="trip" class="pr-3">
                  <v-col cols="12" md="3" class="pa-0" @click="$router.push({name: 'trips-city-slug', params: {city: tripsCity.CityName, slug: trip.packageSlug || trip.packageTitle}})">
                    <v-img height="100%" max-width="350px" :src="trip.packageImage" />
                  </v-col>
                  <v-col cols="12" md="5">
                    <div class="trips-cityname">
                      <span v-for="(item, i) in trip.packageCities" :key="i">
                        {{ item.cityName }}
                        <span v-if="i < trip.packageCities.length - 1">, </span>
                      </span>
                      <br>
                    </div>
                    <div class="cruise-result-trip headline">
                      {{ trip.packageTitle }}<br>
                    </div>
                    <div class="more-text">
                      <p v-html="trip.packageOverview"></p>
                    </div>
                    <v-btn
                      color="blue"
                      dark
                      text
                      small
                      class="px-0"
                      @click="textDialog = true; description = trip.packageOverview; includes = trip.packageIncludes; excludes = trip.packageExcludes"
                    >
                      show Full Description
                    </v-btn>
                  </v-col>
                  <v-col cols="12" md="3" align-self="center">
                    <div v-if="trip.initialPrice && trip.initialPrice !== '0' && trip.initialPrice !== 'null'">
                      Initial Price
                    </div>
                    <div v-if="trip.initialPrice && trip.initialPrice !== '0' && trip.initialPrice !== 'null'" class="hotel-totlal-price">
                      ${{ trip.initialPrice }}
                    </div>
                    <div v-if="trip.starting_airport && trip.starting_airport !== 'null' && trip.starting_airport !== null && trip.starting_airport !== 'undefined'">
                      Package Starting airport
                    </div>
                    <div v-if="trip.starting_airport && trip.starting_airport !== 'null' && trip.starting_airport !== null && trip.starting_airport !== 'undefined'" class="hotel-totlal-price">
                      {{ trip.starting_airport }}
                    </div>
                    <div v-if="trip.packageStartDate && trip.packageStartDate !== 'null'" class="text-right">
                      Recommended Start Date
                    </div>
                    <div v-if="trip.packageStartDate && trip.packageStartDate !== 'null'" class="hotel-totlal-price">
                      {{ trip.packageStartDate }}
                    </div>
                    <div v-if="trip.packageDuration && trip.packageDuration !== '0' && trip.packageDuration !== 'null'">
                      Duration
                    </div>
                    <div v-if="trip.packageDuration && trip.packageDuration !== '0' && trip.packageDuration !== 'null'" class="hotel-totlal-price">
                      {{ trip.packageDuration }} Days
                    </div>
                    <div style="cursor: pointer" class="select-hotel" @click="$router.push({name: 'trips-city-slug', params: {city: tripsCity.citySlug, slug: trip.packageSlug || trip.packageTitle}})">
                      Select
                    </div>
                  </v-col>
                </v-row>
              </div> -->
            </div>
          </div>
          <v-row align="stretch">
            <v-col
              v-for="trip in tripsResults"
              :key="trip.packageID"
              cols="12"
              md="4"
              class="d-flex"
            >
              <v-card class="d-flex flex-column card-container" elevation="2">
                <!-- Image Header -->
                <v-img
                  :src="trip.packageImage.startsWith('http')
                    ? trip.packageImage
                    : `https://api.tanefer.com/storage/package/banner/${trip.packageImage}`"
                  height="200"
                  class="white--text package-card-header"
                >
                  <div class="package-title">
                    {{ trip.packageTitle || "No Title Available" }}
                  </div>
                </v-img>

                <!-- Main Content (Scrollable if needed) -->
                <v-card-text class="flex-grow-1 overflow-hidden">
                  <div class="package-details" style="max-height: 150px; overflow-y: auto;">
                    <div class="detail-item">
                      <v-icon small>
                        mdi-calendar
                      </v-icon>
                      {{ trip.packageNightsNumber || "N/A" }} Nights
                    </div>
                    <div class="detail-item">
                      <v-icon small>
                        mdi-currency-usd
                      </v-icon>
                      Starting From: {{ trip.intialprice }} USD
                    </div>
                    <div v-if="trip.packageOverview" class="detail-item">
                      <v-icon small>
                        mdi-information
                      </v-icon>
                      <!-- Truncated Overview with "See More/Less" -->
                      <TruncatedOverview
                        :overview-html="trip.packageOverview"
                        :max-length="150"
                        :expand-on-click="true"
                      />
                    </div>
                  </div>
                </v-card-text>

                <!-- CTA Button at the Bottom -->
                <v-card-actions>
                  <v-btn
                    color="#bd9468"
                    @click="$router.push({
                      name: 'trips-city-slug',
                      params: { city: tripsCity.citySlug, slug: trip.packageSlug }
                    })"
                  >
                    View Details
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </div>

        <p v-else-if="!trips.length && !$fetchState.pendeing" class="text-center headline font-weight-light">
          Currently, no available packages for this city
        </p>
      </div>

      <p v-else class="text-center headline font-weight-light">
        Couldn't retrieve trips!
      </p>

      <v-dialog
        v-model="textDialog"
        max-width="800"
      >
        <v-card>
          <v-card-title class="text-h5 grey lighten-2">
            Trip Overview
          </v-card-title>

          <v-card-text>
            <p v-html="description"></p>
            <div v-if="includes.length">
              <p class="text-h6">
                Includes
              </p>
              <v-chip v-for="(item, i) in includes" :key="i" class="ma-1">
                {{ item }}
              </v-chip>
            </div>
            <div v-if="excludes.length">
              <p class="text-h6">
                Excludes
              </p>
              <v-chip v-for="(item, i) in excludes" :key="i" class="ma-1">
                {{ item }}
              </v-chip>
            </div>
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import DOMPurify from 'dompurify'
import { mapState } from 'vuex'
import tripsServices from '~/services/tripsServices'

export default {
  ssr: true,
  data () {
    return {
      snackbar: false,
      color: '',
      text: '',
      loaded: true,
      showSearchForm: true,
      showOverview: 'more',
      tripsCount: 0,
      trips: [],
      min: 0,
      max: 0,
      prices: [],
      durations: [],
      // cities: [],
      //   city: {},
      tripLengthes: [],
      tripLength: {},
      lastCity: {},
      textDialog: false,
      description: '',
      includes: [],
      excludes: [],
      tripsResults: [],
      metaData: {
        page_name: null,
        seo_title: null,
        seo_description: null,
        featured_image: null,
        slug: null
      }
    }
  },
  async fetch () {
    const res = tripsServices.getCities('adventure')
    const data = await res
    this.results = data.data.cities
    this.$store.dispatch('setCities', this.results)
    await this.getTrips()
    await this.getTripsFilters()
    this.getMetaData()
  },
  head () {
    return {
      // title: 'Trips in ' + this.$route.params.city
      title: this.metaData.seo_title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.metaData.seo_description
        },
        {
          hid: 'og:title',
          property: 'og:title',
          content: this.metaData.seo_title
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.metaData.seo_description
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: this.metaData.featured_image
        }
      ]
    }
  },
  computed: {
    ...mapState(['tripsCity', 'cities'])
  },
  watch: {
    '$vuetify.breakpoint.smAndDown' (val) {
      if (val) { this.showSearchForm = false } else { this.showSearchForm = true }
    }
  },
  created () {
    this.getMetaData()
  },
  methods: {
    sanitizeHtml (content) {
      return DOMPurify.sanitize(content)
    },
    showFilters () {
      document.getElementById('aside-filter-trip').style.left = 0
      document.getElementById('aside-filter-trip').style.backgroundColor = 'white'
    },
    closeFilter () {
      document.getElementById('aside-filter-trip').style.left = '-100%'
    },
    filteate () {
      const tours = []
      let filtered = []
      // this.$store.state.tripsResults.forEach((trip) => {
      this.tripsResults.forEach((trip) => {
        // if (this.prices[1] >= trip.expected_price && trip.expected_price >= this.prices[0]) {
        //   if (this.durations[1] >= trip.packageDuration && trip.packageDuration >= this.durations[0]) {
        //     tours.push(trip)
        //   }
        // }
        if (this.prices[1] >= trip.initialPrice && trip.initialPrice >= this.prices[0]) {
          if (this.durations[1] >= trip.packageDuration && trip.packageDuration >= this.durations[0]) {
            tours.push(trip)
          }
        }
      })
      if (this.tripLength.nights) {
        tours.forEach((tour) => {
          if (tour.packageNightsNumber === this.tripLength.nights) {
            filtered.push(tour)
          }
        })
      } else { filtered = tours }
      this.trips = filtered
      this.tripsCount = this.tripsResults?.length
    },
    // async getTrips () {
    //   if (this.tripsCity) {
    //     this.$store.dispatch('setTripCity', this.cities.find(el => el.citySlug === this.$route.params.city))
    //     this.lastCity = this.cities.find(el => el.citySlug === this.$route.params.city)
    //   }
    //   // this.$store.dispatch('removeTripsResults')
    //   // this.lastCity = this.$store.state.tripsCity
    //   this.trips = []
    //   const res = tripsServices.getCityTrips(this.tripsCity.CityID)
    //   const results = await res
    //   const trips = results.data.data
    //   // console.log(trips)
    //   // this.$store.dispatch('setTripsResults', trips.tripList)
    //   this.tripsResults = trips.tripList.flatMap(trip => trip)
    //   console.log('trips results', this.tripsResults)
    //   this.trips = this.tripsResults.filter(item => item.intialprice > 0)
    //   // if (trips.tripList.length > 0) {
    //   //   // this.trips = trips.tripList.filter(item => item.expected_price > 0)
    //   //   // this.tripsCount = trips.tripList.filter(item => item.expected_price > 0).length
    //   //   // this.min = trips.tripList.filter(item => item.expected_price > 0)[this.tripsCount - 1].expected_price
    //   //   // this.max = trips.tripList.filter(item => item.expected_price > 0)[0].expected_price
    //   //   // this.trips = trips.tripList.filter(item => item.initialPrice > 0)
    //   //   // this.tripsCount = trips.tripList.filter(item => item.initialPrice > 0).length
    //   //   // this.min = trips.tripList.filter(item => item.initialPrice > 0)[this.tripsCount - 1].initialPrice
    //   //   // this.max = trips.tripList.filter(item => item.initialPrice > 0)[0].initialPrice
    //   //   // this.prices = [this.min, this.max]
    //   //   this.trips = trips.tripList.filter(item => item.initialPrice > 0)
    //   //   this.tripsCount = trips.tripList.filter(item => item.initialPrice > 0).length
    //   //   this.min = trips.tripList.filter(item => item.initialPrice > 0)[this.tripsCount - 1].initialPrice
    //   //   this.max = trips.tripList.filter(item => item.initialPrice > 0)[0].initialPrice
    //   // }
    //   if (trips.tripList.length > 0) {
    //     // Filter trips with valid intialprice > 0
    //     const validTrips = trips.tripList.filter(item => item.intialprice > 0)

    //     // Only proceed if validTrips is not empty
    //     if (validTrips.length > 0) {
    //       this.trips = validTrips
    //       this.tripsCount = validTrips.length
    //       this.min = validTrips[this.tripsCount - 1].intialprice
    //       this.max = validTrips[0].intialprice
    //       this.prices = [this.min, this.max]
    //     } else {
    //     // Handle case where no trips match the filter
    //       this.trips = []
    //       this.tripsCount = 0
    //       this.min = 0
    //       this.max = 0
    //       this.prices = [0, 0]
    //     }
    //   } else {
    //     // Handle case where tripList is empty
    //     this.trips = []
    //     this.tripsCount = 0
    //     this.min = 0
    //     this.max = 0
    //     this.prices = [0, 0]
    //   }
    // },
    async getTrips () {
      if (this.tripsCity) {
        this.$store.dispatch('setTripCity', this.cities.find(el => el.citySlug === this.$route.params.city))
        this.lastCity = this.cities.find(el => el.citySlug === this.$route.params.city)
      }

      // Clear trips before fetching
      this.tripsResults = []

      // Fetch trips
      const res = tripsServices.getCityTrips(this.tripsCity.CityID)
      const results = await res
      const trips = results.data.data

      // Assign tripList directly to tripsResults
      this.tripsResults = trips.tripList

      // Debugging log to verify data
      console.log('Trips results:', this.tripsResults)
    },
    async getTripsFilters () {
      if (this.tripsCity) {
        this.$store.dispatch('setTripCity', this.cities.find(el => el.citySlug === this.$route.params.city))
        this.lastCity = this.cities.find(el => el.citySlug === this.$route.params.city)
      }
      const res = tripsServices.getCityTripsFilters(this.tripsCity.CityID)
      const results = await res
      const filters = await results.data.data
      // this.cities = filters.cities
      if (filters.tripLength.length) {
        this.tripLengthes = filters.tripLength
        this.tripLength = filters.tripLength[0]
        if (this.tripLength) { this.durations = [0, this.tripLength.duration] }
      }
    },
    // async getMetaData () {
    //   try {
    //     const promise = tripsServices.getMetaData(6)
    //     const response = await promise
    //     const results = response.data
    //     // console.log(results)
    //     if (results.data) {
    //       this.metaData = results.data
    //     }
    //   } catch (error) {
    //     this.loaded = false
    //   }
    // }
    async getMetaData () {
      try {
        if (this.lastCity) {
          this.metaData.seo_title = this.lastCity.citySEOTitle ? 'Best Custom ' + this.lastCity.citySEOTitle : null
          this.metaData.seo_description = this.lastCity.citySEODescription ? 'Best Custom ' + this.lastCity.citySEODescription : null
          this.metaData.featured_image = this.lastCity.citySEOImage ? this.lastCity.citySEOImage : null
          this.metaData.slug = this.lastCity.citySlug
        } else if (this.$route.params.city) {
          const res = tripsServices.getCityDetails(this.$route.params.city)
          const results = await res
          const cityDetails = results.data.data
          this.metaData.seo_title = cityDetails.citySEOTitle ? 'Best Custom ' + cityDetails.citySEOTitle : null
          this.metaData.seo_description = cityDetails.citySEODescription ? 'Best Custom ' + cityDetails.citySEODescription : null
          this.metaData.featured_image = cityDetails.citySEOImage ? cityDetails.citySEOImage : null
          this.metaData.slug = cityDetails.citySlug
        }
      } catch (error) {
        this.loaded = false
      }
    }
  }
}
</script>

  <style>
  .package-overview {
    height: 100px;
    overflow-y: hidden;
    margin-bottom: 10px;
    position: relative;
  }

  details {
    overflow: hidden;
  }

  summary {
    display: block;
    position: relative;
    cursor: pointer;
    color: teal;
  }

  .more-text {
    height: 44px;
    overflow: hidden;
  }

  .package-card {
  min-height: 450px;
  border-radius: 10px;
  overflow: hidden;
}

.package-card-header {
  position: relative;
}

.package-title {
  position: absolute;
  bottom: 10px;
  left: 15px;
  font-weight: bold;
  font-size: 1.2rem;
  background-color: rgba(0, 0, 0, 0.5);
  padding: 5px 10px;
  border-radius: 5px;
}

.package-details {
  margin-top: 10px;
}

.detail-item {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
}

.detail-item v-icon {
  margin-right: 5px;
}
  </style>
