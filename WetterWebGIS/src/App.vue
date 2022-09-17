<script setup>
    import CoordinatesComponent from './components/coordinates-component.vue'

    import { reactive } from 'vue'

    const state = reactive(
    {
        message: "Click a place to display it's coordinates",
        x: 0,
        y: 0
    })


    function setMsg(msg)
    {
        state.message  = msg
    }

    function setCoordinates(lat, lon)
    {
        state.x=lat
        state.y=lon
        console.log(state)
    }


    console.log("importing esri");

    import MapView from "@arcgis/core/views/MapView";
    import Map from "@arcgis/core/Map";
    import esriConfig from "@arcgis/core/config";
    import FeatureLayer from "@arcgis/core/layers/FeatureLayer"

    esriConfig.apiKey = "AAPK8edb7de7286343e6a15a84849941c65bIx4YtpaCmxgMNcnWe-tkxXuJTVpNslsh7dt5HSDP4XQF3AwU69V_SRt1ZGnXRPXD";

    const map = new Map({
        basemap: "dark-gray-vector" // Basemap layer service
    });

    const view = new MapView({
        map: map,
        center: [-118.805, 34.027], // Longitude, latitude
        zoom: 13, // Zoom level
        container: "viewDiv" // Div element
    });

    view.popup.autoOpenEnabled = false;
    view.on("click", (event) => {
        // Get the coordinates of the click on the view
        // around the decimals to 3 decimals
        const lat = Math.round(event.mapPoint.latitude * 1000) / 1000;
        const lon = Math.round(event.mapPoint.longitude * 1000) / 1000;

        setMsg("Getroffene Koordinate");
        setCoordinates(lat, lon);
    });


    const proportionalSymbolRenderer = {
        type: "simple",  // autocasts as new SimpleRenderer()
        symbol:
        {
            type: "simple-marker",  // autocasts as new SimpleFillSymbol()
            color: "#800000",
            outline: {  // autocasts as new SimpleLineSymbol()
                  width: 1,
                  color: "white"
                }
        },

        visualVariables:
        [
            {
                type: "size",
                field: "POP",
                stops:
                [
                    { value: 0, size: 3 },
                    { value: 26121000, size: 90 }
                ]
            }
        ]
    }

    const worldCitiesLayer = new FeatureLayer({
        url: "https://services.arcgis.com/P3ePLMYs2RVChkJx/arcgis/rest/services/World_Cities/FeatureServer/0",
        renderer: proportionalSymbolRenderer
    });

    map.add(worldCitiesLayer);


    console.log("done with esri");


</script>

<template>

    <coordinates-component :msg=state.message :x=state.x :y=state.y> </coordinates-component>

</template>


<link rel="stylesheet" href="https://js.arcgis.com/4.24/esri/themes/light/main.css">



