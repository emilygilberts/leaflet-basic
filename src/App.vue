<template>
  <div>
    <div id="map"></div>
  </div>
</template>

<script setup>
import { onMounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css"; // Import Leaflet CSS

onMounted(() => {
  console.log("Initializing map...");

  // Ensure that the #map container is available
  const mapElement = document.getElementById("map");
  if (mapElement) {
    const map = L.map("map").setView([51.505, -0.09], 13);
    L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
      maxZoom: 19,
      attribution:
        '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
    }).addTo(map);
  } else {
    console.error("Map container #map not found");
  }
});
</script>

<style scoped>
#map {
  height: 100vh; /* Adjust height as needed */
  width: 100vw; /* Ensure the map takes full width */
}
</style>
