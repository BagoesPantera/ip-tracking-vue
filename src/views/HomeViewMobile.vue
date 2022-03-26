<script setup>
import { ref } from "vue";
import L from "leaflet";

let searchInput = ref("");
let datas = ref("");
let loading = ref(false);

async function getData() {
  loading.value = true
  const respone =  await fetch(`https://ipapi.co/${searchInput.value}/json`, {
    method: "GET",
  })
  const data = await respone.json();
  datas.value = data;
  loading.value = false;
  // MAP
  // https://github.com/Leaflet/Leaflet/issues/3962#issuecomment-384510881
  var container = L.DomUtil.get('map');
  if(container != null){
    container._leaflet_id = null;
  }
  mapFunc(data.latitude, data.longitude, data.ip);
}
getData();

function mapFunc(lat,lon,ip){
  var map = L.map('map').setView([lat,lon], 12);
  // https://leafletjs.com/SlavaUkraini/reference.html#tilelayer
  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
    tileSize: 512,
    zoomOffset: -1,
  }).addTo(map);
  var marker = L.marker([lat,lon]).addTo(map);
  var circle = L.circle([lat,lon], {
    color: 'red',
    fillColor: '#f03',
    fillOpacity: 0.2,
    radius: 5000
  }).addTo(map);
  marker.bindTooltip(`<center><b>IP Tracking</b></center><hr style="border-top: 3px solid #bbb;">Your IP : ${ip}`);
}

function btnGetdata(value){
  searchInput.value = value;
  getData();
}

// Checking data from json
const apiitems = ["ip", "postal", "latitude", "longitude", "utc_offset", "country_calling_code", "country_area", "country_population"];
function outputCheck(item) {
  for (let index = 0; index < apiitems.length; index++) {
    if (item === apiitems[index]) {
      return true;
    }
  }  
}

</script>
<template>
  <!-- parent div -->
  <div class="flex justify-center items-center h-screen w-screen bg-gradient-to-r from-cyan-500 to-blue-500">
    <!-- centered div -->
    <div class="olumns-2 w-full h-full bg-gray-200 rounded shadow-lg">
        <!-- above div -->
        <div class="h-1/2 w-full">
            <div class="flex items-center w-full mx-auto">
                <span class="inline-block align-middle text-4xl mx-auto text-gray-800 font-bold">IP Tracking</span>
            </div>
            <!-- Search input https://tailwindcomponents.com/component/search-bar -->
            <div class="pt-2 relative mx-auto text-gray-600 w-4/5 h-3/5">
                <input class="border-2 border-gray-300 bg-white h-10 px-5 pr-16 w-full rounded-lg text-sm focus:outline-none" type="search" name="search" placeholder="Search any IP here" v-model="searchInput" @keyup.enter="getData()">
                <button type="submit" class="absolute right-0 top-0 mt-5 mr-4" v-on:click="getData()">
                    <img class="text-gray-600 h-4 w-4 fill-current" src="../assets/icon/search.svg" alt="" width="512px" height="512px">
                </button>
                <!-- OUTPUT -->
                <div class="bg-neutral-700 w-full h-4/5 mt-3 rounded mx-auto my-auto overflow-auto" v-bind:class="{'animate-pulse' : loading, 'a' : !loading}">
                    <div class="" v-if="loading === true">
                        <ul>
                            <li v-for="index in 15">
                                <div class="rounded-full bg-neutral-600 h-5 w-auto mx-2 mt-2" ></div>
                            </li>
                        </ul>
                    </div>
                    <div class="" v-else>
                        <ul class="ml-2">
                            <li v-for="(data, index) in datas">
                            <!-- If api return empty string -->
                                <span v-if="data === ''">
                                <div class="flex">
                                    <p class="text-neutral-300">{{index}}: </p><p class="text-orange-400">&nbsp -</p>    
                                </div>
                                </span>
                                <!-- Number -->
                                <span v-else-if="outputCheck(index)">
                                <div class="flex">
                                    <p class="text-neutral-300">{{index}}: </p><p class="text-green-400">&nbsp {{data}}</p>
                                </div>
                                </span>
                                <!-- Else -->
                                <span v-else>
                                <div class="flex">
                                    <p class="text-neutral-300">{{index}}: </p><p class="text-orange-400">&nbsp {{data}}</p>
                                </div>
                                </span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="text-sm">
                    <button class="h-10 px-3 text-gray-700 transition-colors duration-150 bg-gray-300 rounded-lg focus:shadow-outline hover:bg-gray-400 shadow-lg mt-3 text-xs" @click="btnGetdata('')">My IP</button>
                    <button class="h-10 px-3 text-gray-700 transition-colors duration-150 bg-gray-300 rounded-lg focus:shadow-outline hover:bg-gray-400 shadow-lg mt-3 ml-3 text-xs"  @click="btnGetdata('8.8.8.8')">8.8.8.8</button>
                    <button class="h-10 px-3 text-gray-700 transition-colors duration-150 bg-gray-300 rounded-lg focus:shadow-outline hover:bg-gray-400 shadow-lg mt-3 ml-3 text-xs"  @click="btnGetdata('2001:4860:4860::8888')">2001:4860:4860::8888</button>
                </div>
            </div>
        </div>
        <!-- bottom -->
        <div class="h-1/2 w-full">
             <div id="map" class="w-full h-full"></div>
        </div>
        <!-- end bottom -->
    </div>
  </div>
</template>