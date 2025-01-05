<!-- eslint-disable vue/no-v-html -->
<template>
  <v-container fluid>
    <v-snackbar
      v-model="snackbar"
      :color="color"
      :timeout="5000"
      top
    >
      <v-row>
        <span v-html="text" />
        <v-spacer />
        <v-icon small color="white" class="ml-3" @click="snackbar = false">
          mdi-close
        </v-icon>
      </v-row>
    </v-snackbar>
    <v-container>
      <template v-if="!loading && cruises.length">
        <v-card v-for="item in cruises" :key="item.id" max-width="1200" class="mb-5 px-3 mx-auto">
          <v-row>
            <v-col cols="12" md="5">
              <v-img :src="item.master_image" height="270" class="rounded" />
            </v-col>
            <v-col cols="12" md="7" class="d-flex flex-column justify-space-between">
              <v-card-title class="d-flex justify-space-between flex-wrap align-cener px-0 py-0">
                {{ item.name }} test
                <v-rating
                  background-color="green lighten-2"
                  color="warning"
                  dense
                  empty-icon="mdi-star-outline"
                  full-icon="mdi-star"
                  length="5"
                  readonly
                  size="10"
                  :value="item.stars"
                />
              </v-card-title>
              <v-card-subtitle class="py-0 px-0 my-1">
                sort: {{ item.sort_cruise }}
                Cruise Line: {{ item.cruise_line }} / Ship: {{ item.ship_name }} / Nights: {{ item.number_of_nights }}
              </v-card-subtitle>
              <div class="more-text">
                <!-- eslint-disable-next-line vue/html-self-closing -->
                <p v-html="item.description"></p>
              </div>
              <div>
                <v-btn
                  color="blue"
                  dark
                  text
                  x-small
                  class="px-0 mb-5"
                  @click="textDialog = true; description = item.description"
                >
                  show Full Description
                </v-btn>
              </div>

              <!-- <v-expansion-panels v-if="item.available_dates.length">
                <v-expansion-panel>
                  <v-expansion-panel-header class="pa-1">
                    <h6>This Crusise is available to book during the following dates:</h6>
                  </v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <div v-for="(season, k) in item.available_dates" :key="k">
                      <p><strong>From</strong> {{ season.start_date }} <strong>To</strong> {{ season.end_date }} </p>
                    </div>
                  </v-expansion-panel-content>
                </v-expansion-panel>
              </v-expansion-panels> -->
              <v-card-actions class="pb-0">
                <v-btn color="primary white--text px-5" elevation="0" :loading="showDetailsLoading" @click="getCruise(item.id)">
                  View Details
                </v-btn>
                <v-spacer />
                <v-btn color="lightBrown white--text px-10" elevation="0" :disabled="selectLoading" @click="selectCruise(item.id, item.name)">
                  Select
                </v-btn>
              </v-card-actions>
            </v-col>
          </v-row>
        </v-card>
      </template>
      <template v-else-if="!loading && !cruises.length">
        <p class="text-center text-h6">
          No Cruises Found!
        </p>
      </template>
      <template v-else>
        <v-progress-linear
          indeterminate
          color="yellow darken-2"
        />
      </template>
    </v-container>

    <v-dialog v-if="cruise" v-model="showCruiseDialog" max-width="1200">
      <v-card class="mx-auto" max-width="1200">
        <v-card-title class="lightBrown white--text">
          {{ cruise.name }}
        </v-card-title>
        <v-container>
          <!-- <v-row>
            <v-col cols="12">
              <v-img :src="image" width="100%" class="rounded" />
            </v-col>
            <v-col cols="12" class="d-flex flex-wrap">
              <v-img
                v-for="(item, i) in cruise.images"
                :key="i"
                :src="item.image"
                height="70"
                width="70"
                class="rounded ma-1 gallery-image "
                @click="image = item.image"
              />
            </v-col>
          </v-row> -->
          <div class="gallery-container mb-3">
            <!-- Main Image Display -->
            <div class="main-image-container mb-2">
              <v-img :src="currentMainImage" class="main-image" contain />
              <v-btn
                icon
                class="scroll-btn-left"
                @click="prevImage"
              >
                <v-icon>mdi-chevron-left</v-icon>
              </v-btn>
              <v-btn
                icon
                class="scroll-btn-right"
                @click="nextImage"
              >
                <v-icon>mdi-chevron-right</v-icon>
              </v-btn>
            </div>

            <!-- Thumbnails -->
            <div class="thumbnails-container">
              <v-img
                v-for="(item, i) in cruiseImages"
                :key="i"
                :src="item.image"
                class="thumbnail"
                :class="{ active: currentImageIndex === i }"
                @click="selectImage(i)"
              />
            </div>
          </div>
          <v-card-title class="d-flex justify-space-between flex-wrap align-cener px-0 py-0">
            {{ cruise.name }}
            <v-rating
              background-color="green lighten-2"
              color="warning"
              dense
              empty-icon="mdi-star-outline"
              full-icon="mdi-star"
              length="5"
              readonly
              size="15"
              :value="cruise.stars"
            />
          </v-card-title>
          <v-card-subtitle class="py-0 px-0 my-1">
            sort:  {{ cruise.sort_cruise }} /Cruise Line: {{ cruise.cruise_line }} / Ship: {{ cruise.ship_name }} / Nights: {{ cruise.number_of_nights }} / Cities:
            <span v-for="(item, i) in cruise.cities" :key="i">{{ item.name + ', ' }}</span>
          </v-card-subtitle>
          <v-card-text class="px-0">
            <!-- eslint-disable-next-line vue/html-self-closing -->
            <div v-html="cruise.description"></div>
          </v-card-text>
          <!-- <div v-if="cruise.available_dates.length">
            <h6>This Crusise is available to book during the following dates:</h6>
            <div v-for="(season, k) in cruise.available_dates" :key="k">
              <p><strong>From</strong> {{ new Date(season.start_date.replaceAll('-', '/')).toDateString() }} <strong>To</strong> {{ new Date(season.end_date.replaceAll('-', '/')).toDateString() }} </p>
            </div>
          </div> -->
          <div v-if="cruise.includes.length">
            <p class="text-h6">
              Includes test test
            </p>
            <v-chip v-for="(item, i) in cruise.includes" :key="i" class="ma-1 mobile-chip-font">
              {{ item }}
            </v-chip>
          </div>
          <div v-if="cruise.excludes.length">
            <p class="text-h6">
              Excludes
            </p>
            <v-chip v-for="(item, i) in cruise.excludes" :key="i" class="ma-1 mobile-chip-font">
              {{ item }}
            </v-chip>
          </div>
          <div v-if="cruise.facilities.length">
            <p class="text-h6">
              Facilities
            </p>
            <v-chip v-for="(item, i) in cruise.facilities" :key="i" class="ma-1 mobile-chip-font">
              {{ item }}
            </v-chip>
          </div>
          <div v-if="cruise.policies.length">
            <p class="text-h6">
              Policies
            </p>
            <v-chip v-for="(item, i) in cruise.policies" :key="i" class="ma-1 mobile-chip-font">
              {{ item }}
            </v-chip>
          </div>
          <div v-if="cruise.start_days.length">
            <p class="text-h6">
              Start Days
            </p>
            <v-chip v-for="(item, i) in cruise.start_days" :key="i" class="ma-1 mobile-chip-font">
              {{ item }}
            </v-chip>
          </div>
          <v-card-actions class="pb-0">
            <v-spacer />
            <v-btn color="lightBrown white--text px-10" elevation="0" :disabled="selectLoading" @click="selectCruise(cruise.id, cruise.name)">
              Select
            </v-btn>
          </v-card-actions>
        </v-container>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="textDialog"
      max-width="500"
    >
      <v-card>
        <v-card-title class="text-h5 grey lighten-2">
          Cruise Overview
        </v-card-title>

        <v-card-text>
          <p v-html="description" />
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import { mapState } from 'vuex'
import cruisesServices from '~/services/cruisesServices'

export default {
  beforeRouteUpdate (to) {
    this.$route.query.page = to.query.page
    this.getCruises(to.query.page)
  },
  // eslint-disable-next-line vue/require-prop-types
  props: ['page'],
  data () {
    return {
      cruises: [],
      cruise: null,
      city: {},
      totalPages: 1,
      currentPage: 1,
      loading: true,
      showCruiseDialog: false,
      showDetailsLoading: false,
      image: '',
      currentImageIndex: 0,
      // sort: '',
      color: '',
      snackbar: false,
      text: '',
      selectLoading: false,
      textDialog: false,
      description: '',
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
    // await this.getMetaData()
    await this.getCruises(this.$route.query.page)
  },
  head () {
    return {
      // title: 'Nile cruises in ' + this.$route.params.city
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
    cruiseImages () {
      const images = this.cruise.images || []
      if (this.image) {
        return [{ image: this.image }, ...images] // Include master image
      }
      return images
    },
    currentMainImage () {
      return this.cruiseImages[this.currentImageIndex]?.image || ''
    },
    ...mapState(['tripsCity', 'cities'])
  },
  async created () {
    await this.getMetaData()
  },
  methods: {
    prevImage () {
      if (this.currentImageIndex > 0) {
        this.currentImageIndex -= 1
        this.currentMainImage = this.cruiseImages[this.currentImageIndex].image
      }
    },
    // Move to the next image and highlight the corresponding thumbnail
    nextImage () {
      if (this.currentImageIndex < this.cruiseImages.length - 1) {
        this.currentImageIndex += 1
        this.currentMainImage = this.cruiseImages[this.currentImageIndex].image
      }
    },
    selectImage (index) {
      this.currentImageIndex = index
    },
    scrollThumbnails (direction) {
      const container = this.$refs.thumbnailsScrollable

      if (direction === 'left') {
        container.scrollBy({ left: -100, behavior: 'smooth' })
      } else if (direction === 'right') {
        container.scrollBy({ left: 100, behavior: 'smooth' })
      }
    },
    selectCruise (id, name) {
      // eslint-disable-next-line no-console
      console.log(id, name)
      this.$router.push({ name: 'cruises-city-id', params: { id, city: this.$route.params.city }, query: { cruise: name } })
    },
    async getCruises (page) {
      try {
        let cityId
        // this.$route.query.cityId ? cityId = this.$route.query.cityId : cityId = this.cities.find(city => city.CityName === this.$route.params.city).CityID
        this.$route.query.cityId ? cityId = this.$route.query.cityId : cityId = this.cities.find(city => city.citySlug === this.$route.params.city).CityID
        const promise = cruisesServices.getCityCruises(cityId, parseInt(page) || 1, 10)
        const response = await promise
        const results = response.data
        // eslint-disable-next-line no-console
        // console.log(results.data.cruiseList)
        // console.log(this.$route.query.cityId)
        if (results.data.cruiseList.length > 0) {
          this.cruises = results.data.cruiseList
        } else { this.loading = false }
      } catch (error) {
        this.snackbar = true
        this.color = 'error'
        this.text = 'Something went wrong'
      } finally {
        this.loading = false
      }
    },
    async getCruise (id) {
      try {
        const promise = cruisesServices.getCruiseDetails(id)
        const response = await promise
        const results = response.data
        this.cruise = results.data
        this.image = results.data.master_image
        // this.sort_cruise = results.data.sort_cruise
        this.showCruiseDialog = true
      } catch (error) {
        this.snackbar = true
        this.color = 'error'
        this.text = 'Something went wrong'
        this.loading = false
      } finally {
        this.showDetailsLoading = false
      }
    },
    changePage (page) {
      this.$router.push({ name: 'cruisesResults', query: { page } })
    },
    // async getMetaData () {
    //   try {
    //     const promise = cruisesServices.getMetaData(5)
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
        const getCityDetails = this.cities.find(city => city.citySlug === this.$route.params.city)
        // console.log(getCityDetails)
        if (getCityDetails) {
          this.metaData.seo_title = getCityDetails.citySEOTitle ? 'Best Nile River Cruises & ' + getCityDetails.citySEOTitle : null
          this.metaData.seo_description = getCityDetails.citySEODescription ? 'Best Nile River Cruises & ' + getCityDetails.citySEODescription : null
          this.metaData.featured_image = getCityDetails.citySEOImage ? getCityDetails.citySEOImage : null
          this.metaData.slug = getCityDetails.citySlug
        } else if (this.$route.params.city) {
          const res = cruisesServices.getCityDetails(this.$route.params.city)
          const results = await res
          const cityDetails = results.data.data
          this.metaData.seo_title = cityDetails.citySEOTitle ? 'Best Custom ' + cityDetails.citySEOTitle : null
          this.metaData.seo_description = cityDetails.citySEODescription ? 'Best Custom ' + cityDetails.citySEODescription : null
          this.metaData.featured_image = cityDetails.citySEOImage ? cityDetails.citySEOImage : null
          this.metaData.slug = cityDetails.citySlug
        } else {
          const promise = cruisesServices.getMetaData(5)
          const response = await promise
          const results = response.data
          if (results.data) {
            this.metaData = results.data
          }
        }
      } catch (error) {
        this.loaded = false
      }
    }
  }
}
</script>

<style>
.anchor[data-v-45f35f38] {
  text-align: left;
  padding: 0;
  font-size: smaller;
}
/* .gallery-image {
  cursor: pointer;
  transition: all 0.5s ease-in-out;
}
.gallery-image:hover {
  transform: scale(1.1);
} */
.w-nav {
  z-index: 200;
}
.more-text {
  height: 44px;
  overflow: hidden;
}
@media (max-width: 768px) {
  /* ::v-deep .v-slide-group__wrapper {
    contain: content;
    display: list-item;
    flex: 1 1 auto;
    overflow: hidden;
} */

/* ::deep .v-image__image--cover {
    background-size: contain !important;
} */
.mobile-version-frame {
   margin: 0px;
   padding: 0px;
}
.v-application .pa-3 {
    padding: 3px !important;
}

.mobile-chip-font {
  font-size: 11px !important;
}
}
.gallery-container {
  text-align: center;
  margin: 0 auto;
  max-width: 600px;
  position: relative;
}

.main-image-container {
  position: relative;
  width: 400px; /* Fixed width for the frame */
  height: 300px; /* Fixed height for the frame */
  overflow: hidden;
  border-radius: 8px;
  background-color: #f7f3ef; /* Light background for better appearance */
  display: flex; /* Enable flexbox */
  justify-content: center; /* Horizontally center the image */
  align-items: center; /* Vertically center the image */
  margin: 0 auto; /* Center the container itself within the page */
}

.main-image {
  width: 100%;
  height: 100%;
  object-fit: cover; /* Ensure the image fills the frame */
  transition: transform 0.3s ease-in-out;
}

.main-image:hover {
  transform: scale(1.03); /* Add a slight zoom effect on hover */
}

.scroll-btn-left,
.scroll-btn-right {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background-color: rgba(138, 89, 53, 0.8); /* Light brown background */
  color: white;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.scroll-btn-left:hover,
.scroll-btn-right:hover {
  background-color: rgba(117, 76, 46, 0.9); /* Darker brown on hover */
}

.scroll-btn-left {
  left: 10px;
}

.scroll-btn-right {
  right: 10px;
}

.thumbnails-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(80px, 1fr)); /* Ensures consistent size */
  gap: 10px; /* Space between thumbnails */
}

.thumbnail {
  width: 80px;
  height: 80px;
  object-fit: cover; /* Ensures image fits without distortion */
  border-radius: 8px;
  cursor: pointer;
  border: 2px solid transparent;
  transition: border 0.2s ease;
}

.thumbnail:hover,
.thumbnail.active {
  border-color: #a8814e;
}

</style>
