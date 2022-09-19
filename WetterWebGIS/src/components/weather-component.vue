<script>

    export default
    {
        props:
        {
            ort: String,
            vorhersage: String
        }
    }


</script>



<template>
    <div class="weather-component">
        <h1 class="weather-text"> Wetter für {{ort}}</h1>
        <p class="weather-text"> {{vorhersage}} </p>
    </div>
</template>

<style>
    .weather-component {
        border: 2px solid darkred;
        position: absolute;
        right: 7px;
        bottom: 7px;
        z-index: 2;
        background-color: white;
    }

    .weather-text {
        color: black;
        font-family: "Courier New";
    }

</style>