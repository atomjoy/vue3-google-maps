<script setup>
import { ref } from 'vue'
import MapPolygonDraw from '@/components/map/google/MapPolygonDraw.vue'

let createdPolygon = ref(null)

function getPolygon(e) {
	createdPolygon.value = e
}
</script>

<template>
	<MapPolygonDraw @polygonCreated="getPolygon" :lat="49.299" :lng="19.949" :zoom="5" />
	<p>Polygon {{ createdPolygon }}</p>
</template>
