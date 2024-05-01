<template>
  <v-form ref="form" lazy-validation>
    <v-container fluid>
      <v-row>
        <v-col cols="12" sm="12">
          <v-text-field
            v-model="formData.name"
            label="Full name *"
            required
            min="3"
            max="256"
            :rules="nameRules"
            class="custom-label"
          ></v-text-field>
          <v-text-field
            v-model="formData.email"
            label="E-mail *"
            :rules="emailRules"
            required
            class="custom-label"
          ></v-text-field>
          <template>
            <client-only>
              <VueTelInputVuetify 
              v-model="formData.phone"
              label="Phone *"
              :rules="phoneNumberRules"
              required
              class="custom-label"
              />
            </client-only>
            </template>
          <v-text-field
            v-model="formData.nID"
            label="National ID"
            :rules="formData.nID ? nationalIdRules : []"
            min="10"
            max="10"
            counter
          ></v-text-field>
          <v-file-input
            :label="formData.nID ? 'National ID Attachment * ':'National ID Attachment'"
            v-model="formData.nIDFiles"
            :rules="formData.nID ? fileNIDRules : []"
            show-size
            :class="formData.nID ? 'custom-label':''"
          ></v-file-input>
          <v-file-input
          v-model="formData.avatar"
            label="Avatar Image *"
            prepend-icon="mdi-camera"
            required
            show-size
            class="custom-label"
            :rules="avatarRules"
          ></v-file-input>
          <v-text-field
            v-model="formData.password"
            label="Password *"
            hint="At least 8 characters"
            counter
            required
            type="password"
            min="8"
            class="custom-label"
            :rules="passwordRules"
          ></v-text-field>

          <v-text-field
            v-model="formData.passwordConf"
            type="password"
            label="Password confirmation *"
            hint="At least 8 characters"
            counter
            class="custom-label"
            min="8"
            :rules="formData.password ? passwordConfirmationRules : []"
          ></v-text-field>
        </v-col>
      </v-row>
    </v-container>
    <v-layout justify-center>
      <v-btn type="submit" class="ma-4" @click="nextStep" form="form">Next</v-btn>
    </v-layout>
  </v-form>
</template>
<script>
import VueTelInputVuetify from 'vue-tel-input-vuetify/lib/vue-tel-input-vuetify';
export default {
  components: {
    VueTelInputVuetify
  },
  data: () => ({
    formData: {},
    countryOptions: [],
    isReqNIDF: false,
    nameRules: [(v) => !!v || "Fullname is required"],
    phoneNumberRules: [
      (v) => !!v || "Phone number is required",
      (v) => /^(?:\+\d+|\d+)$/.test(v) || "Invalid phone number format",
    ],
    emailRules: [
      (v) => !!v || "Email is required",
      (v) => /.+@.+\..+/.test(v) || "Email must be valid",
    ],
    nationalIdRules: [
      (v) => /^[0-9]+$/.test(v) || "National ID must contain only numbers",
      (v) => (v && v.length === 10) || "National ID must be 10 digits long",
      (v) =>
        (v && (v.startsWith("1") || v.startsWith("2"))) ||
        "National ID must start with 1 , 2 or 3",
    ],
    fileNIDRules: [
      (v) => !!v || "NID file is required",
      v =>
          !!v && /\.(jpg|jpeg|png|pdf|JPG|JPEG|PNG|PDF)$/i.test(v.name) ||
          "Invalid file format. Only JPG, JPEG, PNG, PDF files are allowed."
      
    ],
    avatarRules: [
      (v) => !!v || "Avatar file is required",
      (v) =>
        (!!v && /\.(jpg|jpeg|png|JPG|JPEG|PNG)$/i.test(v.name)) ||
        "Invalid avatar format. Only JPG, JPEG, PNG files are allowed.",
    ],
    passwordRules: [
      (v) => !!v || "Password is required",
      (v) =>
        (v && v.length >= 8) || "Password must be at least 8 characters long",
      (v) =>
        /[A-Z]/.test(v) ||
        "Password must contain at least one uppercase letter",
      (v) =>
        /[a-z]/.test(v) ||
        "Password must contain at least one lowercase letter",
      (v) =>
        /[!@#$%^&*(),.?":{}|<>]/.test(v) ||
        "Password must contain at least one special character",
    ],
  }),
  created() {},
  computed: {
    passwordConfirmationRules() {
      return [
        (v) => !!v || "Password confirmation is required",
        (v) =>
          v === this.formData.password ||
          "Password confirmation must match the password",
      ];
    },
  },
  methods: {
    nextStep() {
      if (this.$refs.form.validate()) {
        this.$emit("formDataSubmitted", this.formData);
      } else {
        alert("Invalid Form")
      }
    }
  },
};
</script>
<style>
.custom-label .v-label {
  color: #ff5252 !important;
}
</style>