<script>
	// add imports //
	import { onMount, onDestroy } from 'svelte';
	import { browser } from '$app/environment';

	let mapElement;
	let map;
	let L;
	var libData =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/Data/Libraries.geojson';
	var iconSASB =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/SASB.png';
	var iconLPA =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/LPA.png';
	var iconSchomberg =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/Schomburg.png';
	var iconBranch =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/Branch.png';
	var iconBranchLine =
		'https://raw.githubusercontent.com/stahlenstein/NycLibMap/master/static/icons/Branch_line.png';

	onMount(async () => {
		if (browser) {
			// add await imports //
			L = await import('leaflet');
			await import('leaflet.markercluster');

			map = L.map(mapElement, { zoomControl: true, maxZoom: 18, minZoom: 11 }).setView(
				[40.753322, -73.982544],
				11
			);
			var Esri_WorldTopoMap = L.tileLayer(
				'https://server.arcgisonline.com/ArcGIS/rest/services/World_Topo_Map/MapServer/tile/{z}/{y}/{x}',
				{
					attribution:
						'Tiles &copy; Esri &mdash; Esri, DeLorme, NAVTEQ, TomTom, Intermap, iPC, USGS, FAO, NPS, NRCAN, GeoBase, Kadaster NL, Ordnance Survey, Esri Japan, METI, Esri China (Hong Kong), and the GIS User Community'
				}
			);

			var osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			});

			// osmLayer.addTo(map);
			Esri_WorldTopoMap.addTo(map);

			map.createPane('pane_Limits');

			// Load and display your JSON file using Leaflet.js
			fetch(libData)
				.then((response) => response.json())
				.then((data) => {
					//replace Leaflet's default blue marker with a custom icon

					function addLibIcon(feature, latlng) {
						if (feature.properties.name === 'Stephen A. Schwarzman Building') {
							var SASBIcon = L.icon({
								iconSize: [25, 23],
								iconAnchor: [20, 20],
								iconUrl: iconSASB
							});
							var marker = new L.marker(latlng, { icon: SASBIcon })
								.bindTooltip(feature.properties.name, {
									offset: [19, 0]
								})
								.openTooltip();
							return marker;
						}
						if (
							feature.properties.name ===
							'Library for the Performing Arts, Dorothy and Lewis B. Cullman Center'
						) {
							var LPAIcon = L.icon({
								iconSize: [25, 23],
								iconAnchor: [20, 20],
								iconUrl: iconLPA
							});
							var marker = new L.marker(latlng, { icon: LPAIcon })
								.bindTooltip(feature.properties.name, {
									offset: [19, 0]
								})
								.openTooltip();
							return marker;
						}
						if (feature.properties.name === 'Schomburg Center for Research in Black Culture') {
							var SchomburgIcon = L.icon({
								iconSize: [25, 23],
								iconAnchor: [20, 20],
								iconUrl: iconSchomberg
							});
							var marker = new L.marker(latlng, { icon: SchomburgIcon })
								.bindTooltip(feature.properties.name, {
									offset: [19, 0]
								})
								.openTooltip();
							return marker;
						} else {
							var BranchLineIcon = L.icon({
								iconSize: [20, 20],
								iconAnchor: [20, 20],
								iconUrl: iconBranchLine
							});
							var marker = new L.marker(latlng, { icon: BranchLineIcon })
								.bindTooltip(feature.properties.name, {
									offset: [19, 0]
								})
								.openTooltip();
							return marker;
						}
					}

					// create an options object that specifies which function will called on each feature
					let layerOptions = {
						pointToLayer: addLibIcon,
						onEachFeature: function (feature, layer) {
							layer.bindPopup(
								'<h1>' + feature.properties.name + '</h1><p>system: ' + feature.properties.system + '</p>' + '<p>status: ' + feature.properties.status + '<p>'
							);
						}
					};

					L.geoJSON(data, layerOptions)
						// .bindPopup(function (d) {
						// 	return d.feature.properties.name;
						// })
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
