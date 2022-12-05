<script setup>
import { ref, onMounted, defineProps, defineEmits } from 'vue'
import { Loader } from '@googlemaps/js-api-loader'

let api_key = import.meta.env.VITE_GMAP_KEY

const emit = defineEmits(['polygonCreated'])

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
	color: { default: '#e8b54c' },
})

let draw = null
let map = null
let icon = null
let marker = null

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

		// Marker
		marker = new google.maps.Marker({
			position: { lat: props.lat, lng: props.lng },
			title: props.marker.title,
			icon: icon,
			map: map,
		})

		// Draw, Edit, Delete Polygons
		draw = new google.maps.Data({ map: map })
		draw.setControls(['Polygon'])
		draw.setDrawingMode('Polygon')
		draw.setStyle({
			editable: true,
			draggable: true,
			strokeColor: props.color,
			fillColor: props.color,
			strokeOpacity: 0.7,
			strokeWeight: 2,
			fillOpacity: 0.5,
			zIndex: 0,
		})

		// Create
		draw.addListener('addfeature', function (e) {
			updatePolygon(e)
			// Remove old polygon
			draw.forEach((p) => {
				console.log(p, e.feature)
				if (p != e.feature) {
					draw.remove(p)
				}
			})
		})

		// Drag
		draw.addListener('dragend', function (e) {
			updatePolygon(e)
		})

		// Edit
		draw.addListener('setgeometry', function (e) {
			updatePolygon(e)
		})

		// Delete
		draw.addListener('rightclick', function (e) {
			draw.remove(e.feature)
		})

		// Convert polygon to geojson
		const updatePolygon = (e) => {
			let GeoJSON = { type: 'Polygon', coordinates: [] }
			let obj = e.feature.getGeometry()
			let arr = []
			let poly = null
			obj.forEachLatLng((p) => {
				arr.push([p.lng(), p.lat()])
			})
			arr.push(arr[0])
			GeoJSON.coordinates.push(arr)
			poly = JSON.stringify(GeoJSON)
			// Emit to parent
			emit('polygonCreated', poly)
		}
	})
})
</script>

<template>
	<div id="gmap" :style="props.style"></div>
</template>

<style>
/* Add to main.css file if does not work from component scoped */
.gm-style-iw-d div {
	padding: 5px;
}
.gm-style .gm-style-iw {
	color: #000 !important;
	background-color: #fff !important;
	box-shadow: 0 2px 7px 1px rgba(53, 53, 53, 0.3);
	border-radius: 0px;
	overflow: visible !important;
}
.gm-style-iw-d {
	background-color: transparent !important;
	overflow: visible !important;
}
.gm-style .gm-style-iw-tc {
	filter: drop-shadow(0 4px 2px rgba(53, 53, 53, 0.4)) !important;
}
.gm-style .gm-style-iw-tc::after {
	background: #fff !important;
}
.gm-ui-hover-effect > span {
	background-color: #000;
}
.gm-ui-hover-effect:hover > span {
	background-color: #f23 !important;
}

/* Disabe infoWindow max-height */
.gm-style-iw,
.gm-style-iw div,
.gm-style-iw div div,
.gm-style-iw .gm-style-iw-c {
	overflow: visible !important;
	height: auto !important;
	max-height: none !important;
	width: auto;
	max-width: none;
}

/* Info window remove padding */
.gm-style-iw,
.gm-style-iw > div > div {
	padding: 0px !important;
	padding-left: 0px !important;
	padding-top: 0px !important;
	padding-bottom: 0px !important;
	padding-right: 0px !important;
}

/* Info window style */
.iwx {
	position: relative;
	width: 360px !important;
}
.iwx-logo {
	position: absolute;
	top: -50px;
	left: 30px;
	width: 100px !important;
	height: 100px !important;
	border-radius: 50%;
	padding: 10px !important;
	background: #fff;
	z-index: 999;
	overflow: hidden;
}
.iwx-logo img {
	float: left;
	width: 100% !important;
	border-radius: 50%;
}
.iwx h2 {
	display: block;
	font-size: 26px;
	font-weight: 300;
	text-align: center;
	margin-top: 60px;
	margin-bottom: 10px;
}
.iwx a {
	float: left;
	width: 90%;
	margin: 20px 5%;
	padding: 13px 20px;
	color: #fff;
	background: #fdb500;
	border-radius: 10px;
	font-weight: 900;
	font-size: 17px;
	transition: all 0.6s;
}
.iwx a:hover {
	color: #fdb500;
	background: #000;
	border-radius: 50px;
}
</style>
