<script>
	// add imports //
	import { onMount, onDestroy } from 'svelte';
	import { browser } from '$app/environment';

	let mapElement;
	let map;
	var libData ='https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/Data/Libraries.geojson';
	var iconSASB = 'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/SASB.png';
	var iconLPA = 'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/LPA.png';
	var iconSchomberg = 'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/Schomburg.png';
	var iconBranch = 'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/Branch.png';

	onMount(async () => {
		if (browser) {
			// add await imports //
			const L = await import('leaflet');

			map = L.map(mapElement, { zoomControl: true, maxZoom: 18, minZoom: 11 }).setView(
				[40.753322, -73.982544],
				11
			);

			var osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			})
			
			osmLayer.addTo(map);

			map.createPane('pane_Limits');

			// Load and display your JSON file using Leaflet.js
			fetch(libData)
				.then((response) => response.json())
				.then((data) => {
					// replace Leaflet's default blue marker with a custom icon
					// function addLibIcon(feature, latlng) {
					// 	let myIcon = L.icon({
					// 		iconUrl: iconBranch,
					// 		iconSize: [32, 32], // width and height of the image in pixels
					// 		iconAnchor: [12, 12], // point of the icon which will correspond to marker's location
							
					// 	});
					// 	return L.marker(latlng, { icon: myIcon })
					// 	.bindTooltip(feature.properties.name, {
					// 		offset: [19,0]
					// 	}).openTooltip();
					// }

					function addLibIcon(feature, latlng) {
						let markerIcon;
						if (feature.properties.name = 'Stephen A. Schwarzman Building') {
							markerIcon = iconSASB;
						}
						if (feature.properties.name = 'Library for the Performing Arts, Dorothy and Lewis B. Cullman Center') {
							markerIcon = iconLPA;
						}
						if (feature.properties.name = 'Schomburg Center for Research in Black Culture') {
							markerIcon = iconSchomberg;
						}
						else {
							markerIcon = iconBranch
						}
						let myIcon = L.Icon({
							iconUrl: markerIcon,
							iconSize: [32,32],
							iconAnchor: [12,12]
						});
					
						return L.marker(latlng, { icon: myIcon })
						.bindTooltip(feature.properties.name, {
							offset: [19,0]
						}).openTooltip();
					}
					

					// create an options object that specifies which function will called on each feature
					let layerOptions = {
						pointToLayer: addLibIcon
					};

					L.geoJSON(data, layerOptions)
						.bindPopup(function (osmLayer) {
							return osmLayer.feature.properties.name;
						})
						.addTo(map);
					console.log(data);
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
