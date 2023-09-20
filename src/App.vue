<style scoped>
@import "./css/components.css";
@import "./css/mydentist-apps.css";
</style>

<template>
  <div v-if="loaded" class="mydentist-app">
    <div
      id="w-node-_5ef4a456-78ee-3356-a721-49f53fdd5c23-d6f42258"
      class="mydentist-map-wrapper"
    >
      <div
        id="w-node-c1e16b27-3ac3-2b73-8954-fb158ca29dfc-d6f42258"
        class="map-object"
      >
        <GoogleMap
          :api-key="apiKey"
          style="width: 100%; height: 100%"
          :center="coords()"
          :zoom="7"
        >
          <Marker :options="{ position: coords() }" />
        </GoogleMap>
      </div>

      <div v-for="clinic of listClinics.data" class="map-contact-block">
        <div>
          <div class="map-contact-title">
            {{ clinic.attributes.clinic_city }}
          </div>
          <div class="map-contact-text">
            {{ clinic.attributes.clinic_address_1 }}
            {{
              clinic.attributes.clinic_address_2
                ? ", " + clinic.attributes.clinic_address_2
                : ""
            }}

            <br />Tel:
            <a
              class="map-link"
              :href="'tel:' + clinic.attributes.clinic_phone_number"
              >{{ clinic.attributes.clinic_phone_number }}</a
            >

            <a
              class="map-link"
              :href="
                clinic.attributes.clinic_phone_mobile
                  ? 'tel:' + clinic.attributes.clinic_phone_mobile
                  : ''
              "
            >
              {{
                clinic.attributes.clinic_phone_mobile
                  ? ", " + clinic.attributes.clinic_phone_mobile
                  : ""
              }}</a
            >

            <br />Mail:
            <a
              class="map-link"
              :href="'mailto:' + clinic.attributes.clinic_email_address"
              >{{ clinic.attributes.clinic_email_address }}</a
            >
          </div>
        </div>
        <chevronRightSolid
          style="color: #9b1373"
          class="map-arrow-right"
          @click="handleMapArrow"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { GoogleMap, Marker } from "vue3-google-map";
import chevronRightSolid from "./images/chevron-right-solid.vue";

export default {
  name: "App",
  components: { GoogleMap, Marker, chevronRightSolid },

  data() {
    return {
      apiBaseUrl: "https://api.ngine.se/webhook/mydentist/",
      getClinics: "get-clinics",
      userName: "XkehuCfMZ!hU%8h=",
      userPass: "QH5EV=2hNc*LFjJd",
      loaded: false,
      apiKey: import.meta.env.VITE_API_KEY,
    };
  },

  async created() {
    this.listClinics = await this.getApiData(this.apiBaseUrl + this.getClinics);
    this.loaded = true;
    console.log("CLINICS", this.listClinics);
  },

  methods: {
    getApiData(urlEndpoint) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "GET",
          headers: {
            Authorization: "Basic " + btoa(this.userName + ":" + this.userPass),
          },
          redirect: "follow",
        };

        fetch(urlEndpoint, requestOptions)
          .then((response) => {
            if (!response.ok) throw new Error();
            return response.json();
          })
          .then((result) => {
            resolve(result);
          })
          .catch((error) => {
            console.log(error);

            reject(error);
          });
      });
    },

    coords(coords) {
      const lat = this.listClinics.data[0].attributes.latitude;
      const lng = this.listClinics.data[0].attributes.longitude;

      return { lat: Number(lat), lng: Number(lng) };
    },

    handleMapArrow() {},
  },
};
</script>
