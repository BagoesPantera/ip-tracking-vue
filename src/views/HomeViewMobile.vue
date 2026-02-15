<script setup>
import { ref } from "vue";
import L from "leaflet";

let searchInput = ref("");
let datas = ref("");
let loading = ref(false);
let showRateLimit = ref(false);

async function getData() {
  loading.value = true;
  const path = searchInput.value ? `/${searchInput.value}/json` : `/json`;
  let data = {};
  try {
    const respone = await fetch(`https://ipapi.co${path}`, {
      method: "GET",
    });
    if (respone.status === 429) {
      showRateLimit.value = true;
      datas.value = {};
      loading.value = false;
      return;
    }
    data = await respone.json();
    datas.value = data;
  } catch (e) {
    datas.value = {};
  }
  loading.value = false;
  // MAP
  // https://github.com/Leaflet/Leaflet/issues/3962#issuecomment-384510881
  var container = L.DomUtil.get("map");
  if (container != null) {
    container._leaflet_id = null;
  }
  if (data && data.latitude && data.longitude) {
    mapFunc(data.latitude, data.longitude, data.ip);
  }
}
getData();

function mapFunc(lat, lon, ip) {
  var map = L.map("map").setView([lat, lon], 12);
  // https://leafletjs.com/SlavaUkraini/reference.html#tilelayer
  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
    tileSize: 512,
    zoomOffset: -1,
  }).addTo(map);
  var marker = L.marker([lat, lon]).addTo(map);
  var circle = L.circle([lat, lon], {
    color: "red",
    fillColor: "#f03",
    fillOpacity: 0.2,
    radius: 5000,
  }).addTo(map);
  marker.bindTooltip(
    `<center><b>IP Tracking</b></center><hr style="border-top: 3px solid #bbb;">Your IP : ${ip}`,
  );
}

function btnGetdata(value) {
  searchInput.value = value;
  getData();
}

// Checking data from json
const apiitems = [
  "ip",
  "postal",
  "latitude",
  "longitude",
  "utc_offset",
  "country_calling_code",
  "country_area",
  "country_population",
];
function outputCheck(item) {
  for (let index = 0; index < apiitems.length; index++) {
    if (item === apiitems[index]) {
      return true;
    }
  }
}
</script>
<template>
  <div class="min-h-screen w-full bg-slate-50 text-slate-900">
    <header class="w-full border-b border-slate-200 bg-white">
      <div class="px-5 py-4">
        <h1 class="text-lg font-semibold tracking-tight">IP Tracking</h1>
      </div>
    </header>
    <main class="px-5 py-4 space-y-4">
      <section class="rounded-xl border border-slate-200 bg-white shadow-sm">
        <div class="p-4">
          <div class="relative">
            <input
              class="w-full h-11 rounded-lg border border-slate-300 bg-white px-4 pr-12 text-sm placeholder-slate-400 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/20"
              type="search"
              name="search"
              placeholder="Search IP address"
              v-model="searchInput"
              @keyup.enter="getData()"
            />
            <button
              type="submit"
              class="absolute right-2 top-1.5 inline-flex h-8 w-8 items-center justify-center rounded-md border border-slate-300 bg-white text-slate-600 hover:bg-slate-50"
              v-on:click="getData()"
              aria-label="Search"
            >
              <img class="h-4 w-4" src="../assets/icon/search.svg" alt="" />
            </button>
          </div>
          <div class="mt-4 flex gap-2">
            <button
              class="rounded-md border border-slate-300 bg-white px-3 py-2 text-xs text-slate-700 hover:bg-slate-50"
              @click="btnGetdata('')"
            >
              My IP
            </button>
            <button
              class="rounded-md border border-slate-300 bg-white px-3 py-2 text-xs text-slate-700 hover:bg-slate-50"
              @click="btnGetdata('8.8.8.8')"
            >
              8.8.8.8
            </button>
            <button
              class="rounded-md border border-slate-300 bg-white px-3 py-2 text-xs text-slate-700 hover:bg-slate-50"
              @click="btnGetdata('2001:4860:4860::8888')"
            >
              2001:4860:4860::8888
            </button>
          </div>
          <div class="mt-6">
            <div
              class="max-h-80 overflow-auto rounded-lg border border-slate-200"
            >
              <div v-bind:class="{ 'animate-pulse': loading }">
                <div v-if="loading === true" class="p-4">
                  <ul class="space-y-2">
                    <li v-for="index in 12">
                      <div class="h-5 w-full rounded bg-slate-200"></div>
                    </li>
                  </ul>
                </div>
                <div v-else class="divide-y divide-slate-100">
                  <ul>
                    <li
                      v-for="(data, index) in datas"
                      class="flex items-center justify-between px-4 py-2"
                    >
                      <span class="text-sm text-slate-600">{{ index }}</span>
                      <span v-if="data === ''" class="text-sm text-orange-500"
                        >-</span
                      >
                      <span
                        v-else-if="outputCheck(index)"
                        class="text-sm font-medium text-emerald-600"
                        >{{ data }}</span
                      >
                      <span v-else class="text-sm text-slate-800">{{
                        data
                      }}</span>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
      <section class="rounded-xl border border-slate-200 bg-white shadow-sm">
        <div class="p-3">
          <div id="map" class="h-[380px] w-full rounded-lg"></div>
        </div>
      </section>
    </main>
    <div
      v-if="showRateLimit"
      class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/50 px-6"
    >
      <div
        class="w-full max-w-sm rounded-xl border border-slate-200 bg-white p-6 shadow-lg"
      >
        <div class="text-lg font-semibold">Sorry</div>
        <p class="mt-2 text-sm text-slate-600">
          Too many requests. Try again later.
        </p>
        <div class="mt-4 flex justify-end">
          <button
            class="rounded-md border border-slate-300 bg-white px-4 py-2 text-sm text-slate-800 hover:bg-slate-50"
            @click="showRateLimit = false"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
