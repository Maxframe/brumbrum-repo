<template>
    <div class="Map">
        <div ref="container" id="map"></div>
    </div>
</template>

<script scoped>
import mapboxgl from 'mapbox-gl';
import getCoordinatesFromGpxFile from "@/modules/gpx-utilities.js";
import * as turf from '@turf/turf';
import gsap from 'gsap';
import ScrollTrigger from 'gsap/ScrollTrigger';

const CAMERA_ALTITUDE = 1300;
const STEP_LENGTH = 0.0003;
const CAMERA_DISTANCE_BACK = 50*STEP_LENGTH;
let previousMarkerIndex = -1;
let routeCoords;
let camCoords;
let distance = 0;
let globalMap;

/*
Parkplatz: 46.89865,8.2449
Nebelmeer: 8.183884366314318, 46.87901792155232
Nummerschild verloren: 46.87977, 8.14582
Mittagessen: 46.89304, 8.1265
Offroad: 46.91724, 8.11731
der Fall: 46.98443, 8.06942
Ende Gelände 47.02399,8.13162
*/
const testMarker = [
    [8.2449, 46.89865, true],
    [8.183789855919457, 46.878998082489225, true],
    [8.18279849017183, 46.87857932727404, false],
    [8.17897355737293, 46.87974564465177, false],
    [8.14582, 46.87977, true],
    [8.1265, 46.89304, true]];

export default {
    name: 'Map',

    mounted: async function () {
        gsap.registerPlugin(ScrollTrigger);
        mapboxgl.accessToken = 'pk.eyJ1Ijoic2lhbmdpIiwiYSI6ImNrdnRqbGM3ejBzazQyb2x5ZHMwbm5uc2oifQ.VGFNc1MhMDi_q9Uh12oCYg';

        let map = new mapboxgl.Map({
            container: this.$refs.container,
            style: 'mapbox://styles/siangi/ckw65hmof0on415ppqndfa1i2',
            bearing: 90,
            center: [8.244904717943173, 46.898650838469315],
            zoom: 16,
            pitch: 0,
            interactive: false
        });

        let coordinates;
        map.on("load", async function () {
            coordinates = await getCoordinatesFromGpxFile("route");
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
            let camRaw = await getCoordinatesFromGpxFile("camera");

            routeCoords = turf.cleanCoords(turf.lineString(coordinates));
            camCoords = turf.cleanCoords(turf.lineString(camRaw));
            globalMap = map;
            setCameraPosition(map, routeCoords, camCoords, distance);
            
            let positionMarker = new mapboxgl.Marker();
            updateMarker(positionMarker, routeCoords, distance);
            positionMarker.addTo(map);
            createStoryMarkers(map, testMarker);

            gsap.timeline({
                scrollTrigger: {
                    trigger: '#map',
                    start: "top top",
                    end: "70000vh",
                    scrub: 1,
                    onUpdate: self => {
                        update(self, positionMarker);
                    }
                }
            });
        });
    }
}

function update(scrollTrigger, positionMarker){
    if (scrollTrigger.direction > 0){
        moveAlong(globalMap, routeCoords, camCoords, scrollTrigger.progress);
    } else {
        moveBack(globalMap, routeCoords, camCoords, scrollTrigger.progress);
    }
    if (checkIfPosOnMarker(routeCoords, scrollTrigger.progress)){
        handleMarkerHit(routeCoords, scrollTrigger.progress);
    }
    updateMarker(positionMarker, routeCoords, scrollTrigger.progress);
}

function handleMarkerHit(routeLineString, progress){
    let idx = getCloseMarkerIdx(routeLineString, progress);
    if (idx < 0){
        console.error("close marker not found");
        return;
    }

    console.log("new Index:" + idx + "previous index" + previousMarkerIndex);
    if (previousMarkerIndex < 0){
        // handle first scroll
        console.log("first Marker");
        previousMarkerIndex = idx;
        return;
    }

    if (idx > previousMarkerIndex){
        // next Image, or Video
        previousMarkerIndex = idx;
        console.log("next Marker");
        return;
    }

    if (idx < previousMarkerIndex){
        previousMarkerIndex = idx;
        console.log("previous Marker");
        return;
        // previous Image
    }
    /* 
        markerIndex
        Index kleiner oder Grösser als vorheriger Marker?
        anhand von dem Section wechseln
    */
}

function roundCoordinate(coordPart){
    let MULTIPLY_FACT = 1000;
    return Math.round(coordPart * MULTIPLY_FACT)/ MULTIPLY_FACT;
}

function compareCoords(coord1, coord2){
    return ((roundCoordinate(coord1[0]) == roundCoordinate(coord2[0])) && (roundCoordinate(coord1[1]) == roundCoordinate(coord2[1])));
}

function getCloseMarkerIdx(routeLineString, progress){
    let currentCoords = turf.along(routeLineString, turf.lineDistance(routeLineString)* progress).geometry.coordinates;
    for (let idx = 0; idx < testMarker.length; idx++){
        if(compareCoords(currentCoords, testMarker[idx])){
            return idx;
        }
    }

    return -1;
}

function checkIfPosOnMarker(routeLineString, progress){
    let current = turf.along(routeLineString, turf.lineDistance(routeLineString)* progress).geometry.coordinates;
    for (let idx = 0; idx < testMarker.length; idx++){
        if (compareCoords(current, testMarker[idx])){
            return true;
        }
    }

    return false;
}

function createStoryMarkers(map, markerCoords){
    for (let idx = 0; idx < markerCoords.length; idx++){
        if (markerCoords[idx][2]){
            new mapboxgl.Marker().setLngLat([markerCoords[idx][0], markerCoords[idx][1]]).addTo(map);
        }        
    }
}

// calls the helper functions and fills the camera in the map
function setCameraPosition(map, routeLineString, camRouteLineString, distanceTravelled){
    const camera = map.getFreeCameraOptions();
    camera.position = getCameraPos(camRouteLineString, distanceTravelled);
    camera.lookAtPoint(setLookAt(routeLineString, distanceTravelled));
    map.setFreeCameraOptions(camera);
}

function updateMarker(marker, routeLineString, distanceTravelled){
    let lngLat = setLookAt(routeLineString, distanceTravelled);
    marker.setLngLat([lngLat.lng, lngLat.lat]);
}

function moveAlong(map, routeLineString, camRouteLineString, progress){
    setCameraPosition(map, routeLineString, camRouteLineString, progress);
}

function moveBack(map, routeLineString, camRouteLineString, progress){
    setCameraPosition(map, routeLineString, camRouteLineString, progress);
}

// interpolates along the root and returns the proper coordinates
function setLookAt(routeLineString, distanceTravelled){
    let lookAt = turf.along(routeLineString, turf.lineDistance(routeLineString)* distanceTravelled).geometry.coordinates;
    return { lng: lookAt[0], lat: lookAt[1]};
}

function getCameraPos(routeLineString, distanceTravelled){
    let distanceAlong;
    // we can't go backwards outside the route so we wait until the distanceTravelled is greater than Distance back
    if (distanceTravelled <= CAMERA_DISTANCE_BACK){
        distanceAlong = STEP_LENGTH;
    } else {
        distanceAlong = distanceTravelled - CAMERA_DISTANCE_BACK;
    }

    //interpolate along route
    const cameraCoord = turf.along(routeLineString, turf.lineDistance(routeLineString)* distanceAlong).geometry.coordinates;
    // create the proper form of coordinates
    return mapboxgl.MercatorCoordinate.fromLngLat(
        {
            lng: cameraCoord[0],
            lat: cameraCoord[1]
        },
        CAMERA_ALTITUDE);
}
</script>

<style src="mapbox-gl/dist/mapbox-gl.css">
#map{
    position: fixed;
}
</style>
