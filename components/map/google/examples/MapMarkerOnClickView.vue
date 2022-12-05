<script setup>
import { ref } from 'vue'
import MapMarkerOnClick from '@/components/map/google/MapMarkerOnClick.vue'

let marker = ref(null)

function getMarker(e) {
	marker.value = e
}
</script>

<template>
	<MapMarkerOnClick @markerCreated="getMarker" :lat="49.299" :lng="19.949" :zoom="5" />
	<p>Marker {{ marker }}</p>
</template>
