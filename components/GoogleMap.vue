<template>
  <v-container fluid>
    <form @submit.prevent="searchLocation">
        <v-row justify="center">
          <v-col cols="12" sm="8" md="6"> <!-- Adjust the column width as needed -->
            <v-row>
              <v-col>
                <div class="input-field">
                  <input
                    ref="searchInput"
                    v-model="position"
                    placeholder="Search location"
                    @focus="initAutocomplete"
                  />
                </div>
              </v-col>
              <v-col>
                <v-btn type="submit">Search</v-btn>
              </v-col>
            </v-row>
          </v-col>
        </v-row>

    </form>
    <div id="map-container" ref="mapContainer">
      <div id="map"></div>
      <img
        src="https://www.smithandsmith.co.nz/typo3conf/ext/branchlocator/Resources/Public/img/green-map-marker.png"
        class="marker"
        alt=""
      />
    </div>
  </v-container>
</template>

<script>
import googleMapsApiLoader from 'google-maps-api-loader';

export default {
  data() {
    return {
      position: '',
      map: null,
      geocoder: null,
      autocomplete: null,
      observer: null,
    };
  },
  mounted() {
    googleMapsApiLoader({
      apiKey: 'AIzaSyCuTilAfnGfkZtIx0T3qf-eOmWZ_N2LpoY',
      libraries: ['places'],
    }).then((google) => {
      this.map = new google.maps.Map(document.getElementById('map'), {
        center: { lat: -34.397, lng: 150.644 },
        zoom: 11,
      });

      this.geocoder = new google.maps.Geocoder();

      this.observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            this.map.setCenter({ lat: -34.397, lng: 150.644 });
            this.observer.unobserve(this.$refs.mapContainer);
          }
        });
      });

      this.observer.observe(this.$refs.mapContainer);
    });
  },
  methods: {
    initAutocomplete() {
      googleMapsApiLoader({
        apiKey: 'AIzaSyCuTilAfnGfkZtIx0T3qf-eOmWZ_N2LpoY',
        libraries: ['places'],
      }).then((google) => {
        this.autocomplete = new google.maps.places.Autocomplete(this.$refs.searchInput, {
          types: ['geocode'],
        });
      });
    },
    searchLocation() {
      this.geocoder.geocode({ address: this.position }, (results, status) => {
        if (status === 'OK') {
          const location = results[0].geometry.location;
          this.map.setCenter(location);
          if (results[0].geometry.viewport) {
            this.map.fitBounds(results[0].geometry.viewport);
          }
        } else {
          console.log('Geocode was not successful for the following reason: ' + status);
        }
      });
    },
  },
};
</script>

<style>
#map-container {
  position: relative;
  height: 300px;
}

#map {
  width: 100%;
  height: 100%;
}

#searchform {
  padding: 15px;
  display: flex;
}

#searchform input {
  width: 100%;
  height: 40px;
  padding: 0 15px;
}

#searchform button {
  width: auto;
  height: 40px;
  line-height: 40px;
  padding: 0 15px;
  background: teal;
  color: #fff;
}

.marker {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: auto;
  height: 30px;
  margin-top: -15px;
}
.input-field {
  display: inline-block;
  width: 100%;
  margin-bottom: 16px; /* Adjust as needed */
}

.input-field input {
  border: 1px solid rgba(0, 0, 0, 0.42);
  border-top:none;
  border-right:none;
  border-left:none;
  padding: 10px 12px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.3s;
}

.input-field input:focus {
  border-color: #1976d2; /* Adjust to match Vuetify's primary color */
}
</style>
