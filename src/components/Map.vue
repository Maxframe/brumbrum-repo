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
let routeCoords;
let camCoords;
let distance = 0;
let globalMap;

const testMarker = [[8.183884366314318, 46.87901792155232]];

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

            // window.onkeydown = function(event){
            //     if (event.code == "ArrowUp"){
            //         moveAlong(globalMap, routeCoords, camCoords);
            //         updateMarker(positionMarker, routeCoords, distance);
            //     } else if (event.code == "ArrowDown") {
            //         moveBack(globalMap, routeCoords, camCoords);
            //         updateMarker(positionMarker, routeCoords, distance);
            //     }                
            // }
            gsap.timeline({
                scrollTrigger: {
                    trigger: '#map',
                    pin: true,
                    start: "top top",
                    end: "+=300000vh",
                    scrub: 0.5,
                    onUpdate: self => {
                        if (self.direction > 0){
                            moveAlong(globalMap, routeCoords, camCoords, self.progress);
                        } else {
                            moveBack(globalMap, routeCoords, camCoords, self.progress);
                        }
                        if (checkIfPosOnMarker(routeCoords, self.progress)){
                            console.log("hit it!");
                        }
                        updateMarker(positionMarker, routeCoords, self.progress);
                    }
                }
            })

        });
    
    
    }
}
function roundCoordinate(coordPart){
    let MULTIPLY_FACT = 1000
    return Math.round(coordPart * MULTIPLY_FACT)/ MULTIPLY_FACT;
}

function compareCoords(coord1, coord2){
    return ((roundCoordinate(coord1[0]) == roundCoordinate(coord2[0])) && (roundCoordinate(coord1[1]) == roundCoordinate(coord2[1])));
}
function checkIfPosOnMarker(routeLineString, progress){
    let current = turf.along(routeLineString, turf.lineDistance(routeLineString)* progress).geometry.coordinates;
    for (let idx = 0; idx < testMarker.length; idx++){
        return (compareCoords(current, testMarker[idx]))
    }
}

function createStoryMarkers(map, markerCoords){
    for (let idx = 0; idx < markerCoords.length; idx++){
        new mapboxgl.Marker().setLngLat([markerCoords[idx][0], markerCoords[idx][1]]).addTo(map);
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

<style src="mapbox-gl/dist/mapbox-gl.css"></style>
