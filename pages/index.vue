<template>
  <v-row justify="center" align="center">
    <v-col cols="12">
      <template>
        <v-stepper v-model="e1">
          <v-stepper-header>
            <v-stepper-step :complete="e1 > 1" step="1">
              Basic Information
            </v-stepper-step>
            <v-divider></v-divider>
            <v-stepper-step :complete="e1 > 2" step="2">
              Address Information
            </v-stepper-step>
            <v-divider></v-divider>
            <v-stepper-step step="3"> Display Information </v-stepper-step>
          </v-stepper-header>
          <v-stepper-items>
            <v-stepper-content step="1">
              <BasicInfo @formDataSubmitted="handleForm1DataSubmitted" />
            </v-stepper-content>
            <v-stepper-content step="2">
              <GoogleMap/>
              <AddressInfo @formDataSubmitted="handleForm2DataSubmitted"/>
              <v-btn class="ma-4" @click="prevStep">Previous</v-btn>
            </v-stepper-content>
            <v-stepper-content step="3">
              <ShowInfo :formsData="displayForms"/>
              <div class="d-flex justify-center text-center">
                <v-btn type="submit" @click="submitForm" color="success"
                  >Save</v-btn
                >
              </div>
            </v-stepper-content>
          </v-stepper-items>
        </v-stepper>
      </template>
    </v-col>
  </v-row>
</template>

<script>

import BasicInfo from "@/components/basicInfo.vue";
import AddressInfo from '@/components/addressInfo.vue';
import ShowInfo from "@/components/showInfo.vue";
import GoogleMap from "@/components/GoogleMap.vue";

export default {
  name: "IndexPage",
  components: {
    BasicInfo,
    AddressInfo,
    GoogleMap
  },
  data() {
    return {
      e1: 1,
      formDataToShow:{},
      displayForms:[],
      headers: [
        { text: 'Title', value: 'title' },
        { text: 'Value', value: 'value' }
      ]
    };
  },
  computed: {
    filteredHeaders() {
      return this.headers.filter(header => header.value !== 'avatarUrl');
    }
  },
  methods: {
    nextStep() {
      this.e1++;
    },
    prevStep() {
      this.e1--;
    },
    fileToDataUrl(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result);
        reader.onerror = reject;
        reader.readAsDataURL(file);
      });
    },
    async handleForm1DataSubmitted(formData) {
      for (const key in formData) {
        let elm = { title: key, value: formData[key] };
        if (key === 'avatar') {
          try {
            const avatarUrl = await this.fileToDataUrl(formData.avatar);
            elm.avatarUrl = avatarUrl;
          } catch (error) {
            console.error('Error converting file to data URL:', error);
          }
        } else if (key == 'nIDFiles') {
          try {
            const nIDFilesUrl = await this.fileToDataUrl(formData.nIDFiles);
            elm.nIDFilesUrl = nIDFilesUrl;
          } catch (error) {
            console.error('Error converting file to data URL:', error);
          }
        }
        this.displayForms.push(elm);
      }
      this.e1 = 2
    },
    handleForm2DataSubmitted(formData) {
      this.formDataToShow.form2 = formData
      for (const key in formData) {
        let elm = { title: key, value: formData[key] };
        this.displayForms.push(elm);
      }
      this.e1 = 3
    },
    submitForm() {
      console.log("Do any thing with data");
    }
  },
};
</script>
