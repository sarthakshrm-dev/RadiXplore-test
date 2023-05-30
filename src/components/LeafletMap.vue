<template>
  <div class="leaflet-map">
    <div ref="mapContainer" class="map-container"></div>
    <div class="form-container">
      <MarkerForm @form-submit="addMarker"></MarkerForm>
    </div>
  </div>
</template>

<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import { GeoSearchControl, OpenStreetMapProvider } from 'leaflet-geosearch';
import MarkerForm from './MarkerForm.vue';

export default {
  name: 'LeafletMap',
  components: {
    MarkerForm
  },
  data() {
    return {
      searchQuery: '',
      map: null,
      markers: [],
    };
  },
  mounted() {
    this.initializeMap();
    this.addTileLayer();
    this.addSearchControl();
    this.$nextTick(() => {
      this.map.invalidateSize();
    });
  },
  methods: {
    initializeMap() {
      this.map = L.map(this.$refs.mapContainer).setView([0, 0], 2);
    },
    addTileLayer() {
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; OpenStreetMap contributors',
      }).addTo(this.map);
    },
    addSearchControl() {
      const provider = new OpenStreetMapProvider();
      const searchControl = new GeoSearchControl({
        provider,
        showMarker: false,
        showPopup: false,
      });
      this.map.addControl(searchControl);
    },
    addMarker(markerData) {
      const { latitude, longitude, name, description } = markerData;

      const marker = L.marker([latitude, longitude]).addTo(this.map);
      marker.bindPopup(`<b>${name}</b><br>${description}`);
      this.markers.push(marker);

      this.map.setView([latitude, longitude], 4);
    },
  },
};
</script>

<style>
.leaflet-map {
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.map-container {
  flex: 1;
  width: 100%;
  min-height: 300px;
}

.form-container {
  margin: 20px;
}

.reset {
  position: absolute;
  top: 5px;
  right: 5px;
}
</style>