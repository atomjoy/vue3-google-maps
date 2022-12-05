<script setup>
import MapPolygon from '@/components/map/google/MapPolygon.vue'

const polygon = [
	{ lat: 50.87919131500943, lng: 19.793808180377113 },
	{ lat: 49.09947863186494, lng: 22.210800367877113 },
	{ lat: 49.17135924121919, lng: 17.223007399127113 },
	{ lat: 50.87919131500943, lng: 19.793808180377113 },
]
</script>

<template>
	<MapPolygon :lat="49.299" :lng="19.949" :zoom="5" :polygon="polygon" :color="`#f60`" />
</template>
