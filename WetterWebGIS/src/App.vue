<script setup>
    import CoordinatesComponent from './components/coordinates-component.vue'
    import WeatherComponent from './components/weather-component.vue'
    console.log("importing esri");
    import esriConfig from "@arcgis/core/config";
    import Map from "@arcgis/core/Map";
    import MapView from "@arcgis/core/views/MapView";
    import FeatureLayer from "@arcgis/core/layers/FeatureLayer"
    import { reactive } from 'vue'

    var reverse_geocode_api_key = "AIzaSyBiYxJQ82pIaT9bRUPalqubD7Y9QhPApP4";
    var reverse_geocode_api_url = 'https://maps.googleapis.com/maps/api/geocode/json'

    var weather_api_key = "6b904086651c872d0e2c58c1529d2dcb"
    var weather_api_url = "https://api.openweathermap.org/data/2.5/forecast"

    let state = reactive(
    {
        message: "Koordinate per Klick bestimmen",
        x: 0,
        y: 0,
        ort: "unbekannt",
        vorhersage: "unbekannt"
    })

    const map = new Map({
        basemap: "dark-gray-vector" // Basemap layer service
    });

    const view = new MapView({
        map: map,
        center: [-118.805, 34.027], // Longitude, latitude
        zoom: 13, // Zoom level
        container: "viewDiv" // Div element
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

    function main()
    {
        setUpArcGIS()
        setUpComponents()
    }

    function setUpArcGIS()
    {
        setUpConfig()
        setOnClickListener(view)
        addLayer()
        console.log("done with esri");
    }

    function setUpConfig()
    {
        esriConfig.apiKey = "AAPK8edb7de7286343e6a15a84849941c65bIx4YtpaCmxgMNcnWe-tkxXuJTVpNslsh7dt5HSDP4XQF3AwU69V_SRt1ZGnXRPXD";
    }

    function setOnClickListener(view)
    {
        view.on("click", (event) => {
            // Get the coordinates of the click on the view
            // around the decimals to 3 decimals
            const lat = Math.round(event.mapPoint.latitude * 1000) / 1000;
            const lon = Math.round(event.mapPoint.longitude * 1000) / 1000;

            handleSubcomponents(lat, lon)
        });
    }

    function addLayer()
    {
        map.add(worldCitiesLayer);
    }


    function handleSubcomponents(lat,lon)
        {
            setMsg("Getroffene Koordinate")
            setCoordinates(lat, lon)
            makeReverseGeocodeAndWeatherRequest()
        }

    function setMsg(msg)
    {
        state.message  = msg
    }

    function setCoordinates(lat, lon)
    {
        state.x=lat
        state.y=lon
    }

    function setUpComponents()
    {

        console.log('placeholder')

    }

    function makeReverseGeocodeAndWeatherRequest()
    {
        const LOCALITY = 3;
        var request_url = createReverseGeocodeURL()
        console.log(request_url)

        var request = new XMLHttpRequest();
        request.open('GET', request_url, true);

        request.onload = function() {

        if (request.status === 200){
          // Success!
          var data = JSON.parse(request.responseText);

          var ort
          if(data.results[0].address_components[LOCALITY]?.long_name)
            ort = data.results[0].address_components[LOCALITY].long_name
          else
          {
            console.log(data.results[0].address_components[LOCALITY])
            ort = data.results[0].address_components[LOCALITY].short_name
          }


          state.ort = ort
          makeWeatherRequest()

        } else if (request.status <= 500){
          // We reached our target server, but it returned an error

          console.log("unable to geocode! Response code: " + request.status);
          var data = JSON.parse(request.responseText);
          console.log('error msg: ' + data.status.message);
        } else {
          console.log("server error");
        }
        };

        request.onerror = function() {
        // There was a connection error of some sort
        console.log("unable to connect to server");
        };

        request.send();  // make the request
    }

    function createReverseGeocodeURL()
    {
        return reverse_geocode_api_url
           + '?'
           + 'latlng=' + encodeURIComponent(state.x + ',' + state.y)
           + '&key=' + reverse_geocode_api_key
    }

    function makeWeatherRequest()
    {

        var request_url = createWeatherURL()
        console.log(request_url)

        var request = new XMLHttpRequest();
        request.open('GET', request_url, true);

        request.onload = function() {

        if (request.status === 200){
          // Success!

          console.log(request);
          var data = JSON.parse(request.responseText);

          state.vorhersage = data.list[0].dt_txt + ": " + data.list[0].weather[0].description

        } else if (request.status <= 500){
          // We reached our target server, but it returned an error

          console.log("unable to weather! Response code: " + request.status);
        } else {
          console.log("server error");
        }
        };

        request.onerror = function() {
        // There was a connection error of some sort
        console.log("unable to connect to server");
        };

        request.send();  // make the request

    }

    function createWeatherURL()
    {
        return weather_api_url
            + '?'
            + 'q=' + encodeURIComponent(state.ort)
            + '&appid=' + encodeURIComponent(weather_api_key)
    }



    main()


</script>

<template>

    <CoordinatesComponent :msg=state.message :x=state.x :y=state.y>

    </CoordinatesComponent>


    <WeatherComponent :ort=state.ort :vorhersage=state.vorhersage>

    </WeatherComponent>


</template>


<link rel="stylesheet" href="https://js.arcgis.com/4.24/esri/themes/light/main.css">



