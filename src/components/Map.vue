<template>
    <div class="Map">
        <div ref="container" id="map"></div>
    </div>
</template>

<script>
import mapboxgl from 'mapbox-gl';
import getCoordinatesFromGpxFile from "@/modules/gpx-utilities.js"

export default {
    name: 'Map',

    mounted: async function () {
        mapboxgl.accessToken = 'pk.eyJ1Ijoic2FtYnVlbGhlcnpvZyIsImEiOiJja3Z0aWEwaXgwczFkMnZtOTBrcDJhM2dtIn0.wLbpWYbbUQFwlMrIlQ85BQ';

        let map = new mapboxgl.Map({
            container: this.$refs.container,
            style: 'mapbox://styles/sambuelherzog/ckvtiznw8229j15o76fbjedx1',
            bearing: 350,
            center: [8.182855, 46.878608],
            zoom: 11
        });

        let coordinates;
        map.on("load", async function () {
            coordinates = await getCoordinatesFromGpxFile("../assets/incompleteTrack.gpx");
            console.log(coordinates);
            map.addSource("route", {
                type: "geojson",
                data: {
                    type: "Feature",
                    geometry: {
                        type: "LineString",
                        coordinates: coordinates
                    }
                }
            });

            map.addLayer({
                id: "route",
                type: "line",
                source: "route",
                layout: {
                    "line-join": "round",
                    "line-cap": "round",
                },
                paint: {
                    "line-color": "#e1e419",
                    "line-width": 8
                }
            });
        });
    }
}
</script>

<style src="mapbox-gl/dist/mapbox-gl.css"></style>
<style>
   #map {
    height: 50vh;
    width: 100vw;
  } 
</style>
