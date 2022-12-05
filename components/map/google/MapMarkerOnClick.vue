<script setup>
import { ref, onMounted, defineEmits, defineProps } from 'vue'
import { Loader } from '@googlemaps/js-api-loader'

let api_key = import.meta.env.VITE_GMAP_KEY

const emit = defineEmits(['markerCreated'])

const props = defineProps({
	zoom: { default: 5 },
	lat: { required: true, default: 49.29903 },
	lng: { required: true, default: 19.949047 },
	scrollwheel: { default: true },
	disableDefaultUI: { default: false },
	style: { default: 'width: 100%; height: 500px;' },
	marker: {
		default: {
			title: 'Hello!',
			image: 'https://img.icons8.com/fluency/2x/google-maps-new.png',
			width: 70,
			height: 70,
		},
	},
})

let icon = null
let map = null
let new_marker = null
let markers = []

function moveToLocation(lat, lng, map) {
	const center = new google.maps.LatLng(lat, lng)
	map.panTo(center)
}

onMounted(() => {
	// Google map
	const loader = new Loader({
		apiKey: api_key,
		version: 'weekly',
		libraries: ['drawing', 'geometry'],
	})

	// Map loader
	loader.load().then(() => {
		map = new google.maps.Map(document.getElementById('gmap'), {
			center: { lat: props.lat, lng: props.lng },
			zoom: props.zoom,
			scrollwheel: props.scrollwheel,
			disableDefaultUI: props.disableDefaultUI,
		})

		// Marker icon
		icon = {
			url: props.marker.image,
			scaledSize: new google.maps.Size(props.marker.width, props.marker.height),
		}

		// Map event
		google.maps.event.addListener(map, 'click', (e) => {
			const pos = e.latLng
			// Remove old markers
			for (let i = 0; i < markers.length; i++) {
				markers[i].setMap(null)
			}
			// Marker
			new_marker = new google.maps.Marker({
				position: pos,
				map: map,
				icon: icon,
			})
			// Push
			markers.push(new_marker)
			// Center map
			map.panTo(pos)
			// Emit to parent
			emit('markerCreated', pos)
		})
	})
})
</script>

<template>
	<div id="gmap" :style="props.style"></div>
</template>
