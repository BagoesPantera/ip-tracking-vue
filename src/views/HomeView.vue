<script setup>
import { ref } from "vue";

let searchInput = ref("");
let datas = ref("");
let loading = ref(false);
let longitude = ref("");
let latitude = ref("");

async function getData() {
  loading.value = true
  const respone =  await fetch(`http://ipwhois.app/json/${searchInput.value}`, {
    method: "GET",
  })
  const data = await respone.json();
  // console.log(data.longitude);
  longitude.value = data.longitude;
  latitude.value = data.latitude;
  datas.value = data;
  loading.value = false;
}
getData();

function btnGetdata(value){
  searchInput.value = value;
  getData();
}
// number or string mybe
const apiitems = ["ip", "country_phone", "latitude", "longitude", "timezone_dstOffset", "timezone_gmtOffset", "currency_rates", "completed_requests"];
function outputCheck(item) {
  for (let index = 0; index < apiitems.length; index++) {
    if (item === apiitems[index]) {
      return true;
    }
  }  
}

</script>

<template>
  <div class="flex justify-center items-center h-screen w-screen bg-gradient-to-r from-cyan-500 to-blue-500"> <!-- parent div -->
    <div class="w-3/4 h-3/4 flex bg-gray-200 rounded shadow-lg"> <!-- centered div -->
         <!-- LEFT DIV -->
        <div class="grid  w-2/4">
            <!-- Search input https://tailwindcomponents.com/component/search-bar -->
                <div class="pt-2 relative mx-auto mt-5 text-gray-600 w-3/4 h-3/5">
                  <input class="border-2 border-gray-300 bg-white h-10 px-5 pr-16 w-full rounded-lg text-sm focus:outline-none"
                  type="search" name="search" placeholder="Search any IP here" v-model="searchInput" >
                  <button type="submit" class="absolute right-0 top-0 mt-5 mr-4" v-on:click="getData()" >
                    <svg class="text-gray-600 h-4 w-4 fill-current" xmlns="http://www.w3.org/2000/svg"
                      xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" id="Capa_1" x="0px" y="0px"
                      viewBox="0 0 56.966 56.966" style="enable-background:new 0 0 56.966 56.966;" xml:space="preserve"
                      width="512px" height="512px">
                      <path
                        d="M55.146,51.887L41.588,37.786c3.486-4.144,5.396-9.358,5.396-14.786c0-12.682-10.318-23-23-23s-23,10.318-23,23  s10.318,23,23,23c4.761,0,9.298-1.436,13.177-4.162l13.661,14.208c0.571,0.593,1.339,0.92,2.162,0.92  c0.779,0,1.518-0.297,2.079-0.837C56.255,54.982,56.293,53.08,55.146,51.887z M23.984,6c9.374,0,17,7.626,17,17s-7.626,17-17,17  s-17-7.626-17-17S14.61,6,23.984,6z" />
                    </svg>
                  </button>
                  <!-- OUTPUT -->
                  <div class="bg-neutral-700 w-full sm:h-1/5 md:h-2/5 lg:h-3/5 xl:h-4/5 mt-5 rounded mx-auto my-auto overflow-auto" v-bind:class="{'animate-pulse' : loading, 'a' : !loading}">
                    <!-- <div class="w-full h-full bg-white" >
                      <img src="https://cutewallpaper.org/21/loading-gif-transparent-background/Update-throbber-icon-in-Seven-theme-2775725-Drupalorg.gif" width="400" alt="loading image">
                    </div> -->
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
                          <span v-if="index === 'country_flag'">
                            <div class="flex">
                              <p class="text-neutral-300">{{index}}: </p> <img class="rounded ml-2" :src="data" alt="country flag" width="30" height="10">
                            </div>
                          </span>
                          <span v-else-if="outputCheck(index)">
                            <div class="flex">
                              <p class="text-neutral-300">{{index}}: </p><p class="text-green-400">&nbsp {{data}}</p>
                            </div>
                          </span>
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

        <!-- RIGHT DIV -->
        <div class="w-2/4 flex justify-center items-center">
            <div class="w-full h-full">
                <iframe frameborder="0" scrolling="no" marginheight="0" marginwidth="0" :src="`https://www.openstreetmap.org/export/embed.html?bbox=${longitude}%2C${latitude}%2C${longitude}%2C${latitude}&amp;layer=mapnik&amp;marker=${latitude}%2C${longitude}`" style="border: 1px solid black; width: 100%; height: 100%;"></iframe>
                <!-- hmmmmmm zoom : https://help.openstreetmap.org/questions/20335/embedded-html-displays-zoomed-out -->
            </div>
        </div>
    </div>
        <div class="absolute inset-x-0 top-0 h-16 flex justify-center items-center">
           <span class="inline-block align-middle text-4xl text-gray-100 font-bold">IP Tracking</span>
        </div>
       <div class="absolute bottom-0 right-0 h-16 w-100 mr-5 text-gray-100">
         <span>
           Powered by <a class="text-blue-200" href="https://ipwhois.io/" target="_blank">ipwhhois.io</a>
         </span>
        </div>
    </div>
   
</template>
