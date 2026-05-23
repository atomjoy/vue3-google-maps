# Vue3 Google Maps Component

Jak utworzyć marker z popup, poligonu lub marker po kliknięciu w Google map w Vue3.

### Api key .env

Set in .env file (works without key in dev version).

```env
VITE_GMAP_KEY=""
```

## Install google map (simple 3 steps)

```sh
# 1. Add in packages.json in dependencies:
"@googlemaps/js-api-loader": "^1.16.6"

# 2. Copy map dir files to resources/js/map
# Logo public: /default/logo.svg
# Marker public: /default/map/google-marker.png

# 3. And just import and add <Map /> in vue template
```

## Examples

```sh
components/map/google/examples
```

### Add routes

```js
{
  path: '/map/marker',
  name: 'map.marker',
  component: () => import('../components/map/google/examples/MapMarkerView.vue'),
},
{
  path: '/map/polygon',
  name: 'map.polygon',
  component: () => import('../components/map/google/examples/MapPolygonView.vue'),
},
{
  path: '/map/marker/click',
  name: 'map.marker.click',
  component: () => import('../components/map/google/examples/MapMarkerOnClickView.vue'),
},
{
  path: '/map/polygon/draw',
  name: 'map.polygon.draw',
  component: () => import('../components/map/google/examples/MapPolygonDrawView.vue'),
},
```

### Map Marker
<img src="https://raw.githubusercontent.com/atomjoy/vue3-google-maps/main/map.png" width="100%">
