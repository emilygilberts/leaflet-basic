<template>
  <div>
    <input type="file" @change="handleFileUpload" accept=".kml" />
    <div id="map"></div>
  </div>
</template>

<script setup>
import { onMounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css"; // Import Leaflet CSS
import { kml } from "@tmcw/togeojson";

let map;
let layerControl;
onMounted(() => {
  console.log("Initializing map...");
  const map = initMap();
});

const handleFileUpload = (event) => {
  console.log("Uploaded file..");
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      const xml = e.target.result;
      console.log("XML content:", xml);
      const geojson = convertKMLToGeoJSON(xml);
      console.log(geojson);
      createGeoJSONLayerAndAddToMap(geojson);
    };
    reader.onerror = (error) => {
      console.error("Error reading file:", error);
    };
    reader.readAsText(file);
  }
};

const convertKMLToGeoJSON = (input) => {
  const geojson = kml(new DOMParser().parseFromString(input, "text/xml"));
  return geojson;
};

const createGeoJSONLayerAndAddToMap = (geojson) => {
  if (map && layerControl) {
    //create new overlay with geoJSON data
    const geoJSONLayer = L.geoJSON(geojson);
    //add the layer to the map directly
    geoJSONLayer.addTo(map);
    //add layer to layers control so that it can be toggled
    layerControl.addOverlay(geoJSONLayer, "GeoJSON");
    //center the added geojson layer data in the map for preview
    const bounds = geoJSONLayer.getBounds();
    if (bounds.isValid()) {
      map.fitBounds(bounds);
    }
    //TODO: let user approve and persist to db
  } else {
    console.log("map or layeroncontrol is undefined");
  }
};

const initMap = () => {
  var osm = L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
    attribution: "© OpenStreetMap",
  });

  var baseMaps = {
    OpenStreetMap: osm,
  };

  map = L.map("map", {
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
