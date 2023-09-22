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
          ref="mapRef"
          :api-key="apiKey"
          style="width: 100%; height: 100%"
          :center="center"
          :zoom="5.7"
          :styles="mapStyles"
        >
          <Marker
            v-for="(location, index) in listClinics.data"
            :options="{
              position: coords(location),
              icon: 'https://filedn.eu/lXpklx4T9ArjUUtKzxsQ36h/mydentist/mydentist-icon.svg', // google needs complete served image url
            }"
            :key="index"
            @click="handleMarker(index)"
          />
        </GoogleMap>
      </div>

      <div
        v-for="clinic of listClinics.data"
        class="map-contact-block"
        :ref="`${clinic.type}-${clinic.id}`"
        :id="`${clinic.type}-${clinic.id}`"
      >
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
import mapStyles from "./mapstyles.json";

export default {
  name: "App",
  components: { GoogleMap, Marker, chevronRightSolid },

  data() {
    return {
      apiBaseUrl: "https://api.ngine.se/webhook/mydentist/",
      getClinics: "get-clinics",
      getSettings: "get-settings",
      userName: "XkehuCfMZ!hU%8h=",
      userPass: "QH5EV=2hNc*LFjJd",
      loaded: false,
      apiKey: null,
      center: { lat: 59, lng: 16 },
      mapStyles,
    };
  },

  async created() {
    console.clear();

    this.listClinics = await this.getApiData(this.apiBaseUrl + this.getClinics);
    const key = await this.getApiData(this.apiBaseUrl + this.getSettings);
    this.apiKey = key["acf.string"];
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

    coords(location) {
      const lat = location.attributes.latitude;
      const lng = location.attributes.longitude;

      return { lat: Number(lat), lng: Number(lng) };
    },

    handleMarker(index) {
      const clinic = this.listClinics.data[index];
      const refElement = document.getElementById(`${clinic.type}-${clinic.id}`);

      refElement.scrollIntoView({
        behavior: "smooth",
      });
    },

    handleMapArrow() {},
  },
};
</script>

<style scoped>
.map-contact-block:hover .map-arrow-right {
  margin-right: 1rem;
}
</style>
