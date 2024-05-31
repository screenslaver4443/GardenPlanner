<template>
    <h1>Gardening World</h1>
    <label for="width">Width: </label>
    <input id="width" v-on:input="setWidth" type="number" :value="canvasWidth" />
    <label for="height">Height: </label>
    <input id="height" v-on:input="setHeight" type="number" :value="canvasHeight" />
    <div style="border: 1px solid black; width: fit-content">
        <v-stage :config="configKonva">
            <v-layer>
                <v-rect :config="background">
                </v-rect>
            </v-layer>
            <v-layer>
                <v-rect :config="toolbar"></v-rect>
                <v-circle v-bind:key="plant" v-for="plant of plants.length" :config=plants[plant]></v-circle>
            </v-layer>
        </v-stage>
    </div>
</template>
<script>
import jsonData from '@/assets/plants.json'
var canvasWidth = 600
var canvasHeight = 400
export default {
    data() {
        return {
            plantJson: jsonData,
            plantArea: [],
            plantName: [],
            plants: [],
            configKonva: {
                width: canvasWidth,
                height: canvasHeight,
                border: '1px solid black'
            },
            rectExample: {
                x: 0,
                y: 0,
                width: 50,
                height: 50,
                fill: 'red',
                draggable: true
            },
            background: {
                x: 0,
                y: 0,
                width: canvasWidth,
                height: canvasHeight,
                fill: 'green',
            },
            toolbar: {
                width: 75,
                height: canvasHeight,
                fill: 'white',
                stroke: 'black',
                draggable: false
            }
        }
    },
    methods: {
        setWidth() {
            canvasWidth = document.getElementById('width').value
            this.configKonva.width = canvasWidth
            this.background.width = canvasWidth
        },
        setHeight() {
            canvasHeight = document.getElementById('height').value
            this.configKonva.height = canvasHeight
            this.toolbar.height = canvasHeight
            this.background.height = canvasHeight
            this.setToolbar()
        },
        setToolbar() {
            this.plants.splice(0, this.plantJson.length);
            for (var j = 0; j < this.plantJson.length; j++) {
                this.plants.push({
                    x: 40,
                    y: Math.round(this.configKonva.height / this.plantArea.length * j),
                    radius: Math.round(this.plantArea[j]),
                    fill: this.plantJson[j].colour,
                    draggable: true
                })
            }
        },
    },
    props: {},
    created() {
        for (var i = 0; i < this.plantJson.length; i++) {
            this.plantArea.push(this.plantJson[i].minArea)
            this.plantName.push(this.plantJson[i].plant)
        }
        this.setToolbar()
    }
}
</script>

<style>
v-stage {
    border: 1px solid black;
}
</style>
