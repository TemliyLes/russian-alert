<template>
    <div id="map_component">
        <teleport to="body">
            <div class="alert_overlay" v-if="!latLong">
                <WarningIcon />
                <div>Нет доступа к местоположению.</div>
            </div>
        </teleport>
        <div @click="boom">
            Где-то тревога
        </div>
        <div id="map_canvas">
            <l-map ref="mapInstance" v-model:zoom="zoom"
                :center="latLong ? latLong : [51.54572425995206, 43.17877702505453]" :use-global-leaflet="false"
                @ready="onMapReady" :options="mapOptions">
                <l-tile-layer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" layer-type="base"
                    name="OpenStreetMap"></l-tile-layer>
                <l-layer-group ref="markers">
                    <l-marker v-if="latLong" :lat-lng="latLong"></l-marker>

                    <l-circle-marker v-if="alarmCoords" color='red' :lat-lng="alarmCoords" />

                    <l-polyline ref="polyRef" v-if="polyline" :lat-lngs="polyline" color="yellow"></l-polyline>

                </l-layer-group>
            </l-map>
        </div>
    </div>
</template>

<script setup>
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LMarker, LLayerGroup, LCircleMarker, LPolyline } from "@vue-leaflet/vue-leaflet";
import L from 'leaflet';
import { onMounted, ref, watch, computed } from 'vue';
import { Geolocation } from '@capacitor/geolocation';
import WarningIcon from "./WarningIcon.vue";

const mapInstance = ref(null);
const markers = ref(null);
const polyRef = ref(null);

const mapOptions = {
    autoZoom: true,
    maxZoom: 16
}

const zoom = ref(14);
const loc = ref(null);
const latLong = computed(() => loc.value ? [loc.value.lat, loc.value.long] : null)
const polyline = computed(() => alarmCoords.value ? [alarmCoords.value, latLong.value] : null)
const alarmCoords = ref(null);

const getCurrentPosition = async () => {
    const pos = await Geolocation.getCurrentPosition();
    loc.value = {
        lat: pos.coords.latitude,
        long: pos.coords.longitude,
    };
};

const watchPosition = async (e) => {
    try {
        Geolocation.watchPosition({ enableHighAccuracy: true, timeout: 7000 }, (e) => {
            loc.value = {
                lat: e?.coords?.latitude,
                long: e?.coords?.longitude
            }
            // console.log(e?.coords)
        });
    }
    catch (e) {
        console.log(e)
    }

}
const onMapReady = (e) => {

}
onMounted(() => {
    getCurrentPosition();
    watchPosition();
});

const boom = () => {
    const d = [51.52, 46.00];
    if (!alarmCoords.value) {
        alarmCoords.value = d;
    } else {
        alarmCoords.value = null;
    }
}

watch(() => alarmCoords.value, val => {
    const insta = mapInstance.value.leafletObject;
    const dest = val ? val : latLong.value;
    insta.flyTo(dest, 14, {
        duration: 3
    })
})
</script>


<style>
#map_canvas {
    height: calc(100dvh - 120px);
    width: calc(100vw - 12px)
}

.alert_overlay {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 99999;
    width: 100%;
    height: 100dvh;
    background: #111;
    color: #ddd;
    display: grid;
    place-items: center;
}
</style>