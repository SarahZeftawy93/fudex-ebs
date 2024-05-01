<template>
  <v-form ref="form" lazy-validation>
    <v-container fluid>
      <v-row>
        <v-col cols="12" sm="12">
          <v-autocomplete
            v-model="formData.selectedCountry"
            :items="filteredCountries"
            label="Select a country"
            item-text="name"
            item-value="name"
            @change="fetchStates"
          ></v-autocomplete>
          <v-autocomplete
            v-model="formData.selectedState"
            :items="states"
            label="State"
            item-text="name"
            item-value="name"
            @change="fetchCities"
          ></v-autocomplete>
          <v-text-field
            v-model="formData.otherState"
            label="Other State"
            required
            v-if="otherStates"
          ></v-text-field>
          <v-autocomplete
            v-model="formData.selectedCities"
            :items="cities"
            label="Cities"
            @change="fetchOtherCity"
          ></v-autocomplete>
          <v-text-field
            v-model="formData.otherCity"
            label="Other City"
            required
            v-if="otherCities"
          ></v-text-field>
          
        </v-col>
      </v-row>
    </v-container>
    <v-layout justify-center>
      <v-btn class="ma-4" @click="nextStep">Next</v-btn>
    </v-layout>
  </v-form>
</template>
<script>
export default {
  data: () => {
    return {
      countries: [],
      states: [],
      cities: [],
      search: "",
      otherStates:false,
      otherCities:false,
      selectedAddress: '',
      formData:{}
    };
  },
  computed: {
    filteredCountries() {
      return this.countries.filter((country) =>
        country.name.toLowerCase().includes(this.search.toLowerCase())
      );
    },
  },
  created() {
    this.fetchCountries();
  },
  methods: {
    nextStep() {
      this.$emit("formDataSubmitted", this.formData);
    },
    async fetchCountries() {
      try {
        const response = await fetch(
          "https://countriesnow.space/api/v0.1/countries"
        );
        const data = await response.json();

        this.countries = data.data
          .map((country) => ({
            name: country.country,
          }))
          .sort((a, b) => a.name.localeCompare(b.name));
      } catch (error) {
        console.error("Error fetching countries:", error);
      }
    },
    async fetchStates() {
      if (!this.formData.selectedCountry) return;
      try {
        const requestData = {
          order: "asc",
          orderBy: "name",
          country: this.formData.selectedCountry,
        };
        const requestOptions = {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(requestData),
        };

        const response = await fetch(
          "https://countriesnow.space/api/v0.1/countries/states",
          requestOptions
        );
        const data = await response.json();
        if (!data.error) {
          this.states = data.data.states
        }
      } catch (error) {
        console.error("Error fetching states:", error);
      }
      this.states.push('Other')
    },
    async fetchCities() {
      this.otherStates = (this.formData.selectedState == 'Other'?true:false)
      if (!this.formData.selectedState) return;
      if(this.formData.selectedState != 'Other'){
        try {
          const requestData = {
            order: "asc",
            orderBy: "name",
            country: this.formData.selectedCountry,
            state: this.formData.selectedState
          };
          const requestOptions = {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(requestData),
          };
          const response = await fetch(
            "https://countriesnow.space/api/v0.1/countries/state/cities",
            requestOptions
          );
          const data = await response.json();
          this.cities = data.data
        } catch (error) {
          console.error("Error fetching states:", error);
        }
      }
      this.cities.push('Other')
    },
    async fetchOtherCity() {
      this.otherCities = (this.formData.selectedCities == 'Other'?true:false)

    }
  },
};
</script>
