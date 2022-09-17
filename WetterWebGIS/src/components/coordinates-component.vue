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
        <h1 class="text"> {{msg}} </h1>
        <p class = "text">
            latitude:  &nbsp;&nbsp;    {{x}}
        </p>
        <p class = "text">
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
    z-index: 100;
    background-color: white;
}

.text {
    color: black;
    font-family: "Courier New";
}

</style>