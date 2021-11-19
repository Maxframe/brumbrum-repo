<template>
    <div class="Map">
        <div ref="container" id="map"></div>
    </div>
</template>

<script>
import mapboxgl from 'mapbox-gl';
import getCoordinatesFromGpxFile from "@/modules/gpx-utilities.js";
import * as turf from '@turf/turf';

const CAMERA_ALTITUDE = 1400;
const CAMERA_DISTANCE_BACK = 500;
let routeCoords;
let distance = 0;
let globalMap;

export default {
    name: 'Map',

    mounted: async function () {
        mapboxgl.accessToken = 'pk.eyJ1Ijoic2lhbmdpIiwiYSI6ImNrdnRqbGM3ejBzazQyb2x5ZHMwbm5uc2oifQ.VGFNc1MhMDi_q9Uh12oCYg';

        let map = new mapboxgl.Map({
            container: this.$refs.container,
            style: 'mapbox://styles/siangi/ckw65hmof0on415ppqndfa1i2',
            bearing: 270,
            center: [8.182855, 46.878608],
            zoom: 17,
            pitch: 85
        });

        let coordinates;
        map.on("load", async function () {
            coordinates = await getCoordinatesFromGpxFile("../assets/incompleteTrack.gpx");
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
            console.log("loaded")
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
            routeCoords = turf.cleanCoords(turf.lineString(coordinates));
            globalMap = map;
            moveAlongLoop();

        
        });
    
    
    }
}

function setCameraPosition(map, routeLineString, distanceTravelled){
    const camera = map.getFreeCameraOptions();
    camera.position = getCameraPos(routeLineString, distanceTravelled);
    camera.lookAtPoint(getLookAt(routeLineString, distanceTravelled));
    map.setFreeCameraOptions(camera);
}

function moveAlongLoop(){
    setCameraPosition(globalMap, routeCoords, distance);
    distance += 5;

    requestAnimationFrame(moveAlongLoop);
}


function getLookAt(routeLineString, distanceTravelled){
    let cameraCoord = turf.along(routeLineString, distanceTravelled, {units: 'meters'}).geometry.coordinates;
    return { lng: cameraCoord[0], lat: cameraCoord[1]};
}

function getCameraPos(routeLineString, distanceTravelled){
    let distanceAlong;
    if (distanceTravelled <= CAMERA_DISTANCE_BACK){
        distanceAlong = 1;
    } else {
        distanceAlong = distanceTravelled - CAMERA_DISTANCE_BACK;
    }
    console.log(distanceTravelled + " " + distanceAlong)
    const cameraCoord = turf.along(routeLineString, distanceAlong, {units: 'meters'}).geometry.coordinates;
    return mapboxgl.MercatorCoordinate.fromLngLat(
        {
            lng: cameraCoord[0],
            lat: cameraCoord[1]
        },
        CAMERA_ALTITUDE);
}
</script>

<style src="mapbox-gl/dist/mapbox-gl.css"></style>
<style>
   #map {
    height: 95vh;
    width: 100vw;
  } 
</style>
