<script>
	// add imports //
	import { onMount, onDestroy } from 'svelte';
	import { browser } from '$app/environment';

	let mapElement;
	let map;

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

			// Load and display your JSON file using Leaflet.js
			fetch(
				'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/Data/nycLimits.geojson'
			)
				.then(response => response.json())
				.then(data => {
					L.geoJSON(data, {
						style: {
							pane: 'pane_Limits',
							opacity: 1,
							color: 'rgba(188,35,35,1.0)',
							dashArray: '',
							lineCap: 'square',
							lineJoin: 'bevel',
							weight: 5.0,
							fillOpacity: 1,
							interactive: false
						}
					}).addTo(map);
				});

				fetch(
				'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/Data/Library.geojson'
			)
				.then(response => response.json())
				.then(data => {
					L.geoJSON(data).addTo(map);
				});
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
