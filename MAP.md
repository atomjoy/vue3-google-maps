# Google map przykłady

Eventy, funkcje...

## Funkcje

```js
// Czy poligon zawiera punkt
function isContainLocation(event, polygon) {
	return google.maps.geometry.poly.containsLocation(event.latLng, polygon)
}

// Wczytaj plik geojson z polygonem
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

// Dodaj object geojson z polygonem
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
```

## Usuń marker z mapy

```js
marker.setMap(null)
```

## Wyśrodkuj mapę google

```js
function centerMap(lat, lng, map) {
	const center = new google.maps.LatLng(lat, lng)
	map.panTo(center)
}

// Lub z eventu
google.maps.event.addListener(map, 'click', (e) => {
	map.panTo(e.latLng)
})
```

## Dodaj popup po kliknięciu na poligon (events)

```js
let popups = []

google.maps.event.addListener(polygon, 'click', (e) => {
	// Zamknij popup
	popups.forEach((i) => {
		i.close()
	})
	// Polygon infowindow
	const iwp = new google.maps.InfoWindow({
		ariaLabel: 'Position',
	})
	// add to array
	popups.push(iwp)
	// Infowindow html
	const html = '<div class="poly-popup"> Name:' + polygon.details.name + '<br>' + e.latLng.toUrlValue(6) + '</div>'
	// Dodaj do infowindow i wyświetl popup
	iwp.setContent(html)
	iwp.setPosition(e.latLng)
	iwp.open(map)
})
```

## Google map eventy

```js
// Map event
google.maps.event.addListener(map, 'click', (e) => {
	const contain = google.maps.geometry.poly.containsLocation(e.latLng, polygon) ? true : false
	console.log(contain)
})

// Polygon event
google.maps.event.addListener(polygon, 'click', (e) => {
	const contain = google.maps.geometry.poly.containsLocation(e.latLng, polygon) ? true : false
	console.log(contain)
})

// Marker event
marker.addListener('click', () => {
	infowindow.open({ anchor: marker, map: map })
})
```
