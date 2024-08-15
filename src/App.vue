<template>
  <div>
    <div id="map"></div>
  </div>
</template>

<script setup>
import { onMounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css"; // Import Leaflet CSS
import { kml } from "@tmcw/togeojson";

let layerControl;
onMounted(() => {
  console.log("Initializing map...");
  const map = setUpMap();
  fetchAnddConvertLocalKMLToGeoJSON(map);
});

const fetchAnddConvertLocalKMLToGeoJSON = (map) => {
  fetch("./half-dome.kml")
    .then((response) => response.text())
    .then((xml) => {
      //convert xml to kml
      const geojson = kml(new DOMParser().parseFromString(xml, "text/xml"));
      console.log(geojson); // Log the converted GeoJSON
      //create new overlay with geoJSON data
      const geoJSONLayer = L.geoJSON(geojson);
      //add the layer to the map directly
      geoJSONLayer.addTo(map);
      //add layer to layers control so that it can be toggled
      layerControl.addOverlay(geoJSONLayer, "GeoJSON");
      //add geojson layer to layercontrol
    })
    .catch((error) =>
      console.error("Error fetching or processing KML file:", error)
    );
};

const setUpMap = () => {
  var osm = L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
    attribution: "© OpenStreetMap",
  });

  var baseMaps = {
    OpenStreetMap: osm,
  };

  var map = L.map("map", {
    center: [39.73, -104.99],
    zoom: 10,
    layers: [osm],
  });
  layerControl = L.control.layers(baseMaps).addTo(map);
  return map;
};
</script>

<style>
#app {
  padding: 0;
  margin: 0;
  height: 100%;
  width: 100%;
}

#map {
  height: 100vh; /* Adjust height as needed */
  width: 100vw; /* Ensure the map takes full width */
  margin: 0;
  padding: 0;
}
</style>
