<template>
  <h1>Gardening World</h1>
  <label for="width">Width: </label>
  <input id="width" v-on:input="setWidth" type="number" :value="canvasWidth" />
  <label for="height">Height: </label>
  <input id="height" v-on:input="setHeight" type="number" :value="canvasHeight" />
  <button class="full-button" @click="clear">Clear</button>
  <div style="border: 1px solid black; width: fit-content">
    <v-stage :config="configKonva" ref="stage">
      <v-layer>
        <v-rect :config="background"> </v-rect>
      </v-layer>
      <v-layer>
        <v-group
          v-bind:key="plant"
          v-for="plant of plantsGroup.length"
          @dragend="updatePlantPosition(plant, $event)"
          :config="{
            draggable: true,
            x: plantsGroup[plant - 1].x,
            y: plantsGroup[plant - 1].y
          }"
          v-on:dblclick="removePlant(plant)"
        >
          <v-circle :config="plantsShape[plant - 1]"></v-circle>
          <v-image
            :config="{
              x: -plantsShape[plant - 1].radius,
              y: -plantsShape[plant - 1].radius,
              image: plantsImage[plant - 1].image,
              width: plantsShape[plant - 1].radius * 2,
              height: plantsShape[plant - 1].radius * 2
            }"
          ></v-image>
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
      plantImages: [],
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
      }
    }
  },
  methods: {
    setWidth() {
      // Update the width of the canvas and the background rectangle
      canvasWidth = document.getElementById('width').value
      this.configKonva.width = canvasWidth
      this.background.width = canvasWidth
    },
    setHeight() {
      // Update the height of the canvas and the background rectangle
      canvasHeight = document.getElementById('height').value
      this.configKonva.height = canvasHeight
      this.background.height = canvasHeight
    },
    removePlant(plant) {
      // Remove the plant from the arrays
      this.plantsGroup.splice(plant - 1, 1)
      this.plantsShape.splice(plant - 1, 1)
      this.plantsImage.splice(plant - 1, 1)
      this.save() //Call save function to update local storage
    },
    addPlant(plant) {
      // Add the radius and colour of the plant to the arrays
      const plantRad = this.plantArea[this.plantName.indexOf(plant)]
      const plantCol = this.plantJson[this.plantName.indexOf(plant)].colour
      // Add position to array
      this.plantsGroup.push({
        x: canvasWidth / 2,
        y: canvasHeight / 2
      })
      // Add shape to array
      this.plantsShape.push({
        x: 0,
        y: 0,
        radius: plantRad,
        fill: plantCol,
        stroke: 'black'
      })
      // Add image to array
      this.plantsImage.push({
        x: 0,
        y: 0,
        image: this.plantImages[this.plantName.indexOf(plant)],
        width: plantRad,
        height: plantRad
      })
      this.save() // Call save function to update local storage
    },
    clear() {
      // Clear arrays
      this.plantsGroup = []
      this.plantsImage = []
      this.plantsShape = []
      this.save() // Run save function
      location.reload() // Reload Page
    },
    updatePlantPosition(index, event) {
      // Get the current location of
      const newX = event.target.x()
      const newY = event.target.y()
      this.plantsGroup[index - 1].x = newX
      this.plantsGroup[index - 1].y = newY
      this.save()
    },
    save() {
      var imageSrcs = [] // create temporary array to store image locations
      // For loop to move image src's from image object to temporary array
      for (var i in this.plantsImage) {
        imageSrcs.push(this.plantsImage[i].image.src)
      }
      // Convert arrays into JSON Strings and save them in local storage
      localStorage.setItem('group', JSON.stringify(this.plantsGroup))
      localStorage.setItem('shape', JSON.stringify(this.plantsShape))
      localStorage.setItem('imagesrc', JSON.stringify(imageSrcs))
      localStorage.setItem('image', JSON.stringify(this.plantsImage))
    },
    load() {
      // Pull data from JSON string in local storage
      const data1 = localStorage.getItem('group')
      const data2 = localStorage.getItem('shape')
      const data3 = localStorage.getItem('image')
      const data4 = localStorage.getItem('imagesrc')
      // Convert data from JSON string to array
      if (data1) this.plantsGroup = JSON.parse(data1)
      if (data2) this.plantsShape = JSON.parse(data2)
      if (data3) this.plantsImage = JSON.parse(data3)
      if (data4) {
        // Convert data from JSON string to array
        const imageSrcs = JSON.parse(data4)
        // Recreate Image objects from src strings within the array
        for (var i = 0; i < imageSrcs.length; i++) {
          const img = new Image()
          img.src = imageSrcs[i]
          img.onload = ((index) => {
            this.plantsImage[index].image = img
          })(i)
        }
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
    for (var i = 0; i < this.plantJson.length; i++) {
      const img = new Image()
      img.src = this.plantJson[i].image
      img.onload = ((index) => {
        this.plantImages[index] = img
      })(i)
    }
    this.load()
  }
}
</script>

<style>
v-stage {
  border: 1px solid black;
}
</style>
