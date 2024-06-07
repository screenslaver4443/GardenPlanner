<template>
  <h1>Gardening World</h1>
  <label for="width">Width: </label>
  <input id="width" v-on:input="setWidth" type="number" :value="canvasWidth" />
  <label for="height">Height: </label>
  <input id="height" v-on:input="setHeight" type="number" :value="canvasHeight" />
  <div style="border: 1px solid black; width: fit-content;">
    <v-stage :config="configKonva">
      <v-layer>
        <v-rect :config="background"> </v-rect>
      </v-layer>
      <v-layer>
        <v-group v-bind:key="plant" v-for="plant of plantsGroup.length" :config="{
          draggable: true,
          x: plant.x,
          y: plant.y,
        }">
          <v-circle :config="plantsShape[plant - 1]"></v-circle>
          <v-image :config="{
            x: -plantsShape[plant - 1].radius,
            y: -plantsShape[plant - 1].radius,
            image: image,
            width: plantsShape[plant - 1].radius * 2,
            height: plantsShape[plant - 1].radius * 2,
          }"></v-image>
        </v-group>
      </v-layer>
    </v-stage>
  </div>
  <div>
    <ul>
      <button v-for="plant in plantName" v-bind:key="plant" @click="addPlant(plant)">
        Add {{ plant }}
      </button>
    </ul>
  </div>
</template>
<script>
import jsonData from '@/assets/plants.json'
var canvasWidth = 800
var canvasHeight = 600
export default {
  data() {
    return {
      plantJson: jsonData,
      plantArea: [],
      plantName: [],
      plantsGroup: [],
      plantsShape: [],
      plantsImage: [],
      configKonva: {
        width: canvasWidth,
        height: canvasHeight,
        border: '1px solid black'
      },
      background: {
        x: 0,
        y: 0,
        width: canvasWidth,
        height: canvasHeight,
        fill: '#3F9B0B'
      },
      image: null,
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
    addPlant(plant) {
      const plantRad = this.plantArea[this.plantName.indexOf(plant)]
      const plantCol = this.plantJson[this.plantName.indexOf(plant)].colour
      this.plantsGroup.push({
        x: canvasWidth / 2,
        y: canvasHeight / 2,
      })
      this.plantsShape.push({
        x: 0,
        y: 0,
        radius: plantRad,
        fill: plantCol,
        stroke: 'black'
      })
      this.plantsImage.push({
        x: 0,
        y: 0,
        image: this.plantJson[this.plantName.indexOf(plant)].image,
        width: plantRad,
        height: plantRad,
      })
    },
    setToolbar() {
      this.plants.splice(0, this.plantJson.length)
      for (var j = 0; j < this.plantJson.length; j++) {
        this.plants.push({
          x: 40,
          y: Math.round((this.configKonva.height / this.plantArea.length) * j),
          radius: Math.round(this.plantArea[j]),
          fill: this.plantJson[j].colour,
          draggable: true
        })
      }
    }
  },
  props: {},
  created() {
    for (var i = 0; i < this.plantJson.length; i++) {
      this.plantArea.push(this.plantJson[i].minArea)
      this.plantName.push(this.plantJson[i].plant)
    }
  },
  mounted() {
    // Load image (replace with your image URL)
    const img = document.createElement("img");
    img.src = this.plantJson[0].image;
    img.onload = () => {
      this.image = img;
    }
  },
}
</script>

<style>
v-stage {
  border: 1px solid black;
}
</style>
