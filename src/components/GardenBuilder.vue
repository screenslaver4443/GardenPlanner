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
        <v-group v-bind:key="plant" v-for="plant of plantsGroup.length" @dragend="updatePlantPosition(plant, $event)"
          :config="{
            draggable: true,
            x: plantsGroup[plant - 1].x,
            y: plantsGroup[plant - 1].y
          }" v-on:dblclick="removePlant(plant)">
          <v-circle :config="plantsShape[plant - 1]"></v-circle>
          <v-image :config="{
            x: -plantsShape[plant - 1].radius,
            y: -plantsShape[plant - 1].radius,
            image: plantsImage[plant - 1].image,
            width: plantsShape[plant - 1].radius * 2,
            height: plantsShape[plant - 1].radius * 2
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
      canvasWidth = document.getElementById('width').value
      this.configKonva.width = canvasWidth
      this.background.width = canvasWidth
    },
    setHeight() {
      canvasHeight = document.getElementById('height').value
      this.configKonva.height = canvasHeight
      this.background.height = canvasHeight
    },
    removePlant(plant) {
      this.plantsGroup.splice(plant - 1, 1)
      this.plantsShape.splice(plant - 1, 1)
      this.plantsImage.splice(plant - 1, 1)
      this.save()
    },
    addPlant(plant) {
      const plantRad = this.plantArea[this.plantName.indexOf(plant)]
      const plantCol = this.plantJson[this.plantName.indexOf(plant)].colour
      this.plantsGroup.push({
        x: canvasWidth / 2,
        y: canvasHeight / 2
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
        image: this.plantImages[this.plantName.indexOf(plant)],
        width: plantRad,
        height: plantRad
      })
      this.save()
    },
    clear() {
      this.plantsGroup = []
      this.plantsImage = []
      this.plantsShape = []
      this.save()
      location.reload()
    },
    updatePlantPosition(index, event) {
      const newX = event.target.x()
      const newY = event.target.y()
      this.plantsGroup[index - 1].x = newX
      this.plantsGroup[index - 1].y = newY
      this.save()
    },
    save() {
      // const imageSrcs = this.plantsImage.map(img => img.src);
      var imageSrcs = []
      for (var i in this.plantsImage) {
        imageSrcs.push(this.plantsImage[i].image.src)
      }
      localStorage.setItem('group', JSON.stringify(this.plantsGroup))
      localStorage.setItem('shape', JSON.stringify(this.plantsShape))
      localStorage.setItem('imagesrc', JSON.stringify(imageSrcs))
      localStorage.setItem('image', JSON.stringify(this.plantsImage))
    },
    load() {
      const data1 = localStorage.getItem('group')
      if (data1) this.plantsGroup = JSON.parse(data1)
      const data2 = localStorage.getItem('shape')
      if (data2) this.plantsShape = JSON.parse(data2)
      const data3 = localStorage.getItem('image')
      if (data3) this.plantsImage = JSON.parse(data3)
      const data4 = localStorage.getItem('imagesrc')
      if (data4) {
        const imageSrcs = JSON.parse(data4);
        // Recreate Image objects from src strings
        for (var i = 0; i < imageSrcs.length; i++) {
          const img = new Image()
          img.src = imageSrcs[i]
          img.onload = ((index) => {
            this.plantsImage[index].image = img
          })(i)
        }
      }
    },
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
