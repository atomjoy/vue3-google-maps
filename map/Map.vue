<script setup>
// Add in packages.json in dependencies:
// "@googlemaps/js-api-loader": "^1.16.6",

import { onMounted, defineProps } from 'vue';
import { Loader } from '@googlemaps/js-api-loader';
import settings from '@/map/settings.json';
import gstyle from '@/map/style.json';
// import { useI18n } from 'vue-i18n';

let api_key = import.meta.env.VITE_GMAP_KEY;

// const { t } = useI18n({ useScope: 'global' });
const props = defineProps({
    lat: { default: settings.marker.lat },
    lng: { default: settings.marker.lng },
    company: { default: settings.marker.title },
    about: { default: settings.marker.about },
    email: { default: settings.marker.email },
    mobile: { default: settings.marker.mobile },
    website: { default: settings.marker.website_url },
    website_text: { default: settings.marker.website_txt },
    zoom: { default: settings.marker.zoom ?? 13 },
    scrollwheel: { default: settings.marker.scrollwheel ?? false },
    disableDefaultUI: { default: false },
    style: { default: 'width: 100%; height: 500px;' },
    marker: {
        default: {
            title: settings.marker.title,
            image: '/default/map/google-marker.png',
            width: 70,
            height: 70,
        },
    },
    text_mobile: { default: '<i class="fa-solid fa-phone"></i>' },
    text_email: { default: '<i class="fa-solid fa-envelope"></i>' },
    content: {
        default: `
        <div class="iwx">
        <div class="iwx-logo"><img src="/default/logo.svg" alt="Logo"></div>
        <h2>Company</h2>
        <div><i class="fa-solid fa-phone"></i> <a href="tel:+41 000 600 700" title="Phone">+41 000 600 700</a></div>
        <div><i class="fa-solid fa-envelope"></i> <a href="mailto:email@example.com" title="Email">email@example.com</a></div>
        <a class="iwx-btn" href="https://example.com" title="Link">See More</a>
        </div>
        `,
    },
});

let map = null;
let icon = null;
let marker = null;
let infowindow = null;
let content = `
<div class="iwx">
    <div class="iwx-logo"><img src="/default/logo.svg" alt="Logo"></div>
    <h2>${props.company}</h2>
    <div>${props.about}</div>
    <div>${props.text_mobile} <a href="tel:${props.mobile}" title="Phone">${props.mobile}</a></div>
    <div>${props.text_email} <a href="mailto:${props.email}" title="Email">${props.email}</a></div>
    <a target="_blank" class="iwx-btn" href="${props.website}" title="Link">${props.website_text}</a>
</div>
`;

onMounted(() => {
    // Google map
    const loader = new Loader({
        apiKey: api_key,
        version: 'weekly',
        // libraries: ['drawing', 'geometry', 'places'],
    });
    // Settings
    const mapOptions = {
        center: { lat: props.lat, lng: props.lng },
        zoom: props.zoom,
        scrollwheel: props.scrollwheel,
        disableDefaultUI: props.disableDefaultUI,
        styles: gstyle,
        // mapStyle: {}
    };
    // Map loader
    loader
        .importLibrary('maps')
        .then(async ({ Map }) => {
            map = new google.maps.Map(
                document.getElementById('gmap'),
                mapOptions,
            );

            // Info window
            infowindow = new google.maps.InfoWindow({
                // content: props.content,
                content: content,
                ariaLabel: props.marker.title,
            });

            // Marker icon
            icon = {
                url: props.marker.image,
                scaledSize: new google.maps.Size(
                    props.marker.width,
                    props.marker.height,
                ),
            };

            // Marker
            marker = new google.maps.Marker({
                position: { lat: props.lat, lng: props.lng },
                title: props.marker.title,
                icon: icon,
                map: map,
            });

            // Marker event
            marker.addListener('click', () => {
                // Open infowindow
                infowindow.open({
                    anchor: marker,
                    map: map,
                });
            });
        })
        .catch((e) => {
            console.log('Map error', e);
        });
});
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
.gm-ui-hover-effect {
    margin-right: 10px !important;
    margin-top: 10px !important;
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
    border-radius: 0px;
    padding: 10px !important;
    background: #fff;
    z-index: 999;
    overflow: hidden;
}
.iwx-logo img {
    float: left;
    width: 100% !important;
}
.iwx h2 {
    display: block;
    font-size: 26px;
    font-weight: 900;
    text-align: left;
    margin-top: 60px;
    margin-bottom: 5px;
    padding: 0px 20px;
}
.iwx a.iwx-btn {
    float: left;
    width: auto;
    margin: 20px 5%;
    padding: 13px 20px;
    color: #fff;
    background: #000;
    border-radius: 10px;
    font-weight: 900;
    font-size: 17px;
    transition: all 0.6s;
}
.iwx a.iwx-btn:hover {
    color: #fff;
    background: #444;
    border-radius: 50px;
}
.iwx div {
    display: block;
    font-size: 16px;
    font-weight: 400;
    text-align: left;
    margin-top: 5px;
    margin-bottom: 5px;
    padding: 5px 20px;
}
</style>
