<template>
  <div>
    <h1>Live Location Tracker with Leaflet</h1>
    <div v-for="(location, index) in locations" :key="index" class="map-container">
      <div :id="'map-' + index" class="map"></div>
    </div>
  </div>
</template>

<script>
import { onMounted, ref, nextTick } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import axios from "axios";

export default {
  setup() {
    const locations = ref([]);

    const initMap = (id, lat, lon) => {
      const map = L.map(id).setView([lat, lon], 13);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 19,
      }).addTo(map);
      L.marker([lat, lon]).addTo(map);
    };

    const startTracking = () => {
      axios.get(
        "https://api-palembang.teknosof.com/kas_palembang/PostLoc/getLoc",
        {
          headers: {
            "disini-key": "accessapidisini",
          },
        }
      ).then((res) => {
        locations.value = res.data.results;
        nextTick(() => {
          locations.value.forEach((location, index) => {
            initMap('map-' + index, parseFloat(location.lat), parseFloat(location.long));
          });
        });
      }).catch((err) => {
        console.log(err);
      });
    };

    onMounted(() => {
      startTracking();
    });

    return { locations };
  }
}
</script>

<style scoped>
.map-container {
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.map {
  height: 400px;
  width: 100%;
}
</style>
