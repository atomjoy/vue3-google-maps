<script setup>
import { ref, onMounted, defineProps } from 'vue'
import { Loader } from '@googlemaps/js-api-loader'

let api_key = import.meta.env.VITE_GMAP_KEY

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
	content: {
		default: `
    <div class="iwx">
      <div class="iwx-logo"><img src="https://img.freepik.com/free-vector/final-sale-yellow-with-offer-details_1017-30356.jpg?w=740&t=st=1670156691~exp=1670157291~hmac=5fc071cbde354e3de5e90fb32232afe8484c6e92a7604675170d50782f9b0dd6"></div>
      <h2>Hello how are you today?</h2>
      <a class="iwx-btn" href="https://www.falcon-nw.com">See more</a>
    </div>
    `,
	},
	color: { default: '#e8b54c' },
	polygon: {
		default: [
			{ lat: 50.87919131500943, lng: 19.793808180377113 },
			{ lat: 49.09947863186494, lng: 22.210800367877113 },
			{ lat: 49.17135924121919, lng: 17.223007399127113 },
			{ lat: 50.87919131500943, lng: 19.793808180377113 },
		],
	},
})

let map = null
let icon = null
let marker = null
let infowindow = null
let polygon = null

// If polygon includes location point
function isContainLocation(event, polygon) {
	return google.maps.geometry.poly.containsLocation(event.latLng, polygon)
}

// Load geojson polygon
function loadGeoJson(path = '/pl.json') {
	map.data.loadGeoJson(path)
	map.data.setStyle({
		fillColor: '#5c5',
		strokeColor: '#5c5',
		strokeWeight: 3,
	})
	map.data.addListener('click', function (event) {
		map.data.overrideStyle(event.feature, { fillColor: '#f60' })
	})
}

// Add geojson polygon
function addGeoJson(obj) {
	map.data.addGeoJson(obj)
	map.data.setStyle({
		fillColor: '#5c5',
		strokeColor: '#5c5',
		strokeWeight: 3,
	})
	map.data.addListener('click', function (event) {
		map.data.overrideStyle(event.feature, { fillColor: '#f60' })
	})
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

		// Info window
		infowindow = new google.maps.InfoWindow({
			content: props.content,
			ariaLabel: props.marker.title,
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

		// Marker event
		marker.addListener('click', () => {
			// Open infowindow
			infowindow.open({
				anchor: marker,
				map: map,
			})
		})

		// Polygon
		polygon = new google.maps.Polygon({
			paths: props.polygon,
			fillColor: props.color,
			fillOpacity: 0.35,
			strokeColor: props.color,
			strokeOpacity: 0.8,
			strokeWeight: 2,
		})

		// Set polygon on map
		polygon.setMap(map)
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
