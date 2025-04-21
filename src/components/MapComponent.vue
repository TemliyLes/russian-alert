<template>
    <div id="map_component">
        MAP COMPONENT {{ loc }}
        <div id="map_canvas">
            <l-map ref="map" v-model:zoom="zoom"
                :center="loc ? [loc.lat, loc.long] : [51.54572425995206, 43.17877702505453]"
                :use-global-leaflet="false">
                <l-tile-layer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" layer-type="base"
                    name="OpenStreetMap"></l-tile-layer>
                <l-marker v-if="loc" :lat-lng="[loc.lat, loc.long]"></l-marker>
            </l-map>
        </div>
    </div>
</template>

<script setup>
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";
import { onMounted, ref } from 'vue';
import { Geolocation } from '@capacitor/geolocation';

const zoom = ref(14);
const loc = ref(null);

const getCurrentPosition = async () => {
    const pos = await Geolocation.getCurrentPosition();
    loc.value = {
        lat: pos.coords.latitude,
        long: pos.coords.longitude,
    };
};

const watchPosition = async (e) => {
    Geolocation.watchPosition({ enableHighAccuracy: true, timeout: 7000 }, (e) => {
        loc.value = {
            lat: e.coords.latitude,
            long: e.coords.longitude
        }
    });
}

onMounted(() => {
    getCurrentPosition();
    watchPosition();
})
</script>


<style>
#map_canvas {
    height: calc(100dvh - 120px);
    width: calc(100vw - 12px)
}
</style>