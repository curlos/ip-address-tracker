<template>
  <div class="wrapper">
    <div class="topHeader">
      <div class="headerTitle">IP Address Tracker</div>

      <div class="searchInputWrapper">
        <input type="text" placeholder="Search for any IP address or domain" v-model="searchText" v-on:keyup.enter="fetchIpAddress"/>
        <button @click="fetchIpAddress">
          <img src="/assets/images/icon-arrow.svg" alt="arrow" />
        </button>
      </div>

      <div class="ipAddressInfo">
        <div class="ipAddressCard">
          <div class="title">IP ADDRESS</div>
          <div v-if="ipAddress.ip" class="info">{{ ipAddress.ip }}</div>
        </div>

        <div class="ipAddressCard">
          <div class="title">LOCATION</div>
          <div v-if="ipAddress.location" class="info">
            {{ ipAddress.location.city }}, {{ ipAddress.location.region }}
          </div>
        </div>

        <div class="ipAddressCard">
          <div class="title">TIMEZONE</div>
          <div v-if="ipAddress.location" class="info">UTC {{ ipAddress.location.timezone }}</div>
        </div>

        <div class="ipAddressCard">
          <div class="title">ISP</div>
          <div v-if="ipAddress.isp" class="info">{{ ipAddress.isp }}</div>
        </div>
        
        
      </div>
    </div>

    <div v-if="ipAddress.ip" class="locationMap">
      <l-map
        v-model="zoom"
        v-model:zoom="zoom"
        :center="[ipAddress.location.lat, ipAddress.location.lng]"
        @move="log('move')"
        class="map"
      >
        <l-tile-layer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        ></l-tile-layer>
        <l-control-layers />
        <l-marker :lat-lng="[ipAddress.location.lat, ipAddress.location.lng]" draggable @moveend="log('moveend')">
          <l-tooltip>
            {{ ipAddress.location.city }}, {{ ipAddress.location.region }}
          </l-tooltip>
        </l-marker>
      </l-map>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import {
  LMap,
  LTileLayer,
  LMarker,
  LControlLayers,
  LTooltip
} from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css";

export default {
  name: 'App',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LControlLayers,
    LTooltip
  },
  mounted() {
    axios.get(`https://geo.ipify.org/api/v2/country,city?apiKey=${process.env.VUE_APP_API_KEY}&ipAddress=8.8.8.8`).then((response) => {
      console.log(response.data)
      this.ipAddress = response.data
    })
  },
  data() {
    return {
      ipAddress: {},
      searchText: '',
      loading: false
    }
  },
  methods: {
    async fetchIpAddress() {
      if (this.searchText) {
        console.log('fetching...')
        this.loading = true
        console.log(this.searchText)

         axios.get(`https://geo.ipify.org/api/v2/country,city?apiKey=${process.env.VUE_APP_API_KEY}&ipAddress=${this.searchText}`)
         .then((response) => {
          console.log(response.data)
          this.ipAddress = response.data
        })
        .catch((err) => {
          console.error(err)
        })
      }
    }
  },
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');

  * {
    margin: 0;
    box-sizing: border-box;
  }

  .wrapper {
    height: 100vh;
    width: 100vw;
    max-width: 100%;
    font-family: Rubik, sans-serif;
  }

  .topHeader {
    background-image: url('/assets/images/pattern-bg.png');
    background-size: cover;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
    padding: 15px;
  }

  .headerTitle {
    font-weight: medium;
    color: #FFFFFF;
    font-size: 32px;
    letter-spacing: -0.29px;
  }

  .searchInputWrapper {
    width: 50%;
    border-radius: 10px;
    display: flex;
    align-items: center;
  }

  .searchInputWrapper input {
    width: 100%;
    height:100%;
    border-top-left-radius: 10px;
    border-bottom-left-radius: 10px;
    border: none;
    padding: 15px 20px;
    font-size: 18px;
    font-family: Rubik, sans-serif;
    box-sizing: border-box;
  }

  .searchInputWrapper input:focus {
    outline: none;
  }

  ::-webkit-input-placeholder { /* Chrome/Opera/Safari */
    color: #969696;
  }
  ::-moz-placeholder { /* Firefox 19+ */
    color: #969696;
  }
  :-ms-input-placeholder { /* IE 10+ */
    color: #969696;
  }
  :-moz-placeholder { /* Firefox 18- */
    color: #969696;
  }

  .searchInputWrapper button {
    padding: 16px;
    background-color: #000000;
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
    height: 100%;
    border: none;
    font-size: 18px;
    cursor: pointer;
  }

  .ipAddressInfo {
    padding: 20px 5px;
    background-color: #FFFFFF;
    border-radius: 10px;
    border: noen;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    box-shadow: 0 0 10px gray;
    width: 80%;
    margin-bottom: -60px;
  }

  .ipAddressCard {
    font-size: 20px;
    padding-left: 20px;
    padding-right: 30px;
    border-right: 1px solid #D9D9D9;
  }

  .ipAddressCard:last-of-type {
    border-right: none;
  }

  .ipAddressCard .title {
    color: #969696;
    font-size: 12px;
    font-weight: bold;
    letter-spacing: 1.75px;
    margin-bottom: 5px;
  }

  .ipAddressCard .info {
    font-size: 26px;
    font-weight: medium;
    color: #2C2C2C;
    letter-spacing: -0.23px;
  }

  .locationMap {
    height: 100%;
    width: 100vw;
    margin-top: 60px;
  }

  @media only screen and (max-width: 768px) {
    .searchInputWrapper {
      width: 100%;
    }

    .ipAddressInfo {
      display: block;
      width: 100%;
      text-align: center;
    }
  }


</style>
