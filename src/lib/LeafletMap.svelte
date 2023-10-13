<script>
	// add imports //
	import { onMount, onDestroy } from 'svelte';
	import { browser } from '$app/environment';

	let mapElement;
	let map;
	const libData = 'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/Data/Libraries.geojson';

	onMount(async () => {
		if (browser) {
			// add await imports //
			const L = await import('leaflet');

			map = L.map(mapElement, { zoomControl: true, maxZoom: 18, minZoom: 11 }).setView(
				[40.753322, -73.982544],
				11
			);

			L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			}).addTo(map);

			map.createPane('pane_Limits');

			function style(feature) {
				return {
					fillColor: "#000000",
					weight: 1,
					opacity: 1,
					color: "#ffffff",
					fillOpacity: 0.5
				};
			}

			function onEachFeature(feature, layer) {
				L.tooltip([feature.geometry.coordinates[0],feature.geometry.coordinates[1]], {
					content: feature.properties.name,
					permanent: false,
					direction: Auto,
					offset: [10, 0]
				});
				// L.icon({
				// 	iconUrl: 
				// })
			};


			// Load and display your JSON file using Leaflet.js
				fetch(libData)
				.then(response => response.json())
				.then(data => {
					L.geoJSON(data, {
						style: style,
						onEachFeature: onEachFeature
					})
					.addTo(map);
					console.log(data);
				});



				var tooltip = L.tooltip([meter.GPS.Latitude, meter.GPS.Longitude], {
						content: meter.GPS.Address,
						permanent: false,
						direction: 'auto',
						offset: [10, 0]
					});

				const markerIcon = L.icon({
						iconUrl: `https://raw.githubusercontent.com/Main-FCWD/FloydWebMap/main/static/Data/markers/rt${GPSRoute}.svg`,
						iconSize: [25, 25] // Adjust the size according to your marker images
					});

				const marker = L.marker([meter.GPS.Latitude, meter.GPS.Longitude], { icon: markerIcon })
						.bindTooltip(tooltip)
						.openTooltip();
		}
	});

	onDestroy(async () => {
		if (map) {
			console.log('Unloading Leaflet map.');
			map.remove();
		}
	});
</script>

<main>
	<div bind:this={mapElement} />
</main>

<style>
	@import 'leaflet/dist/leaflet.css';
	main div {
		height: 100vh;
	}
</style>
