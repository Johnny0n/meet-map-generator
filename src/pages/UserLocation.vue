<template>
  <div>
    <section class="ui two column centered grid" style="position: relative;z-index: 1;">
      <div class="column">
        <form class="ui segment large form">
          <div class="ui message red" v-show="error">{{error}}</div>
          <div class="ui segment">
            <div class="field">
              <div class="ui right icon input large" :class="{loading:spinner}">
                <input
                  type="text"
                  placeholder="Enter your address"
                  v-model="address"
                  ref="autocomplete"
                />
                <i class="map marker alternate icon" @click="locatorButtonPressed"></i>
              </div>
            </div>
            <button class="ui button">Go</button>
          </div>
        </form>
      </div>
    </section>
    <section id="map"></section>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      address: "",
      error: "",
      spinner: false
    };
  },


  methods: {
    locatorButtonPressed() {
      this.spinner = true;

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          position => {
            this.getAddressFrom(
              position.coords.latitude,
              position.coords.longitude
            );
          },
          error => {
            this.error =
              "Locator is unable to find your address. Please type your address manually.";
            this.spinner = false;
            // console.log(error.message);
          }
        );
      } else {
        this.error = error.message;
        this.spinner = false;
        console.log("Your browser does not support geolocation API ");
      }
    },
    getAddressFrom(lat, long) {
      axios
        .get(
          "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
          lat +
          "," +
          long +
          "&key=AIzaSyBDiJRVKlBL25S2NSesC7VOkhXfi30vK1I"
        )
        .then(response => {
          if (response.data.error_message) {
            this.error = response.data.error_message;
            console.log(response.data.error_message);
          } else {
            this.address = response.data.results[0].formatted_address;
            // console.log(response.data.results[0].formatted_address);
          }
          this.spinner = false;
        })
        .catch(error => {
          this.error = error.message;
          this.spinner = false;
          console.log(error.message);
        });
    },
    // showUserLocationOnTheMap(latitude, longitude){
    //   // map object
    //
    //   let map = new google.maps.Map(document.getElementById("map"), {
    //     zoom: 15,
    //     center: new google.maps.LatLng(latitude, longitude),
    //     mapTypeId:google.maps.MapTypeId.ROADMAP
    //   });
    //
    //   // add Marker
    // },
  }
};
</script>

<style>
.ui.button,
.map.marker.alternate.icon {
  background-color: dodgerblue;
  color: white;
}
#map{
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: lightblue;
}
</style>



