<template>
    <h1>Gardening World</h1>
</template>
<script>
import jsonData from "@/assets/plants.json";
export default {
    data() {
        return {
            plantJson: jsonData,
            areaById: [],
            nameById: [],
        }
    },
    methods: {
    },
    props: {
    },
    created() {
        for (var i = 0; i < this.plantJson.length; i++) {
            this.areaById.push({ key: this.plantJson[i].id, value: this.plantJson[i].minArea })
            this.nameById.push({ key: this.plantJson[i].id, value: this.plantJson[i].plant })
        }
    },

}
</script>


<style></style>