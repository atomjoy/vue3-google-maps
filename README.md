# Vue3 Google Maps komponenty

Jak utworzyć marker z popup, poligonu lub marker po kliknięciu w Google map w Vue3.

### Instalacja google api

```sh
npm i @googlemaps/js-api-loader
```

### Przekopiuj katalog z komponentami

```sh
components/map/google
```

### Dodaj klucz do api w pliku vite .env

```sh
VITE_GMAP_KEY=""
```

### Przykłady w katalogu

```sh
components/map/google/examples
```

### Dodaj przykłady do routera

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
