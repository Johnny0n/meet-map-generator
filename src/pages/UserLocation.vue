<template>
  <div>
    <section class="ui one column centered grid map-second ">
      <div class="column ">
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
            <div class="centered">
              <button class="ui button">Go</button>
            </div>

          </div>
          <input
            type="text"
            placeholder="Type your couple inicials"
            @input="locatorButtonPressed()"
            v-model="initials"
          />
        </form>
      </div>
    </section>

    <section id="map" ref="map"></section>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      address: "",
      initials:"Type your Initials",
      error: "",
      spinner: false,
    };
  },

  mounted() {
    var autocomplete = new google.maps.places.Autocomplete(
      this.$refs["autocomplete"],
    );

    autocomplete.addListener("place_changed", () => {
      var place = autocomplete.getPlace();

      this.showLocationOnTheMap(
        place.geometry.location.lat(),
        place.geometry.location.lng()
      );
    });
  },

  methods: {
    locatorButtonPressed() {
      this.spinner = true;

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.getAddressFrom(
              position.coords.latitude,
              position.coords.longitude
            );

            this.showLocationOnTheMap(
              position.coords.latitude,
              position.coords.longitude
            );
          },
          (error) => {
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
          "&key=Apikey"
        )
        .then((response) => {
          if (response.data.error_message) {
            this.error = response.data.error_message;
            console.log(response.data.error_message);
          } else {
            this.address = response.data.results[0].formatted_address;
            // console.log(response.data.results[0].formatted_address);
          }
          this.spinner = false;
        })
        .catch((error) => {
          this.error = error.message;
          this.spinner = false;
          console.log(error.message);
        });
    },

    showLocationOnTheMap(latitude, longitude) {
      // Show & center the Map based oon
      var map = new google.maps.Map(this.$refs["map"], {
        zoom: 15,
        center: new google.maps.LatLng(latitude, longitude),
        mapId:'map_key',
        backgroundColor: 'hsla(0, 0%, 0%, 0)', //transparent
      });

      new google.maps.Marker({

        position: new google.maps.LatLng(latitude, longitude),
        map: map,
        label: {
          text: this.initials,
          color: "#4682B4",
          fontSize: "30px",
          fontWeight: "bold",
          labelStyle: {opacity: 0.50},
          raiseOnDrag: true,
          className: 'marker-position',
        },
        disableDefaultUI: true,
        raiseOnDrag: true,
        draggable: true,
        keyboardShortcuts: false,
        disableDoubleClickZoom: true,
        noClear: true,
        backgroundColor: 'hsla(0, 0%, 0%, 0)', //transparent
        // icon: 'http://meet-map.jspace.pl/love.png',
        // icon: 'http://meet-map.jspace.pl/love-white-big.png',
        icon: 'http://meet-map.jspace.pl/love-blue-big.png',
        // icon: 'http://meet-map.jspace.pl/love-magenta.png',
        // icon: 'http://meet-map.jspace.pl/love-green.png',
        // icon: 'http://meet-map.jspace.pl/love-red.png',
        // icon: 'http://meet-map.jspace.pl/love-yellow.png',
      });

    },
  },
};
</script>


<style>
/*Btn i marker location*/
.ui.button,
.map.marker.alternate.icon {
  background-color: dodgerblue;
  color: white;
}
/*Mapa*/
#map{
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  /*background-color: lightblue;*/
}
.map-second{
  position: relative;
  z-index: 1;
}

/*Marker*/

.marker-position {
  bottom: -2em;
  left: 0;
  background-color:rgba(0, 0, 0, 0.8);
  position: relative;
  white-space: nowrap;
  padding: 5px;
  text-shadow: 4px 4px 2px rgba(256,256,256,0.2);
}

/*Drop down google*/
.pac-icon {
  display:none;
}

.pac-item {
  padding:10px;
  font-size: 16px;
  cursor: pointer;
}

.pac-item:hover {
  background-color: rgba(30, 144, 255, 0.1);
}

.pac-item-query {
  font-size: 16px;
}
/*google options on map*/
.gmnoprint,.gm-style-cc{
  display: none !important;
}
</style>



