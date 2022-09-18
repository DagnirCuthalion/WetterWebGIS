<script setup>
    defineProps
    ({
        msg: String,
        x: Number,
        y: Number
    })
</script>



<template>
    <div class="coordinates-component">
        <h1 class="coordinates-text"> {{msg}} </h1>
        <p class = "coordinates-text">
            latitude:  &nbsp;&nbsp;    {{x}}
        </p>
        <p class = "coordinates-text">
            longitude: &nbsp;          {{y}}
        </p>
    </div>
</template>

<style>
.coordinates-component {
    border: 2px solid darkred;
    position: absolute;
    right: 7px;
    top: 7px;
    z-index: 2;
    background-color: white;
}

.coordinates-text {
    color: black;
    font-family: "Courier New";
}

</style>