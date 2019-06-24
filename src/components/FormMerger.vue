<template>
    <v-card hover class="pa-2 ma-2 blue darken-4 transparent">
        <v-form lazy-validation>
        <v-select v-model="selectedModel"
            :items="modelChoices" label="Model" required>
        </v-select>
        <v-slider v-model="numIterations" label="Nombre d'itération"
            min="10" max="250" thumb-label="always" color="black">
        </v-slider>
    </v-form>
    </v-card>
</template>


<script lang="ts">
import Vue from 'vue'
import { Component } from 'vue-property-decorator'

@Component
export default class FormMerger extends Vue {

    modelChoices = ['VGG16', 'VGG19']
    selectedModel = 'VGG16'
    numIterations = '200'

    retrieveForm () : {selectedModel: string, numIterations: number} | null  {
        if (this.selectedModel && this.numIterations)
            return {
                selectedModel: this.selectedModel,
                numIterations: parseInt(this.numIterations)
            }
        return null
    }

}
</script>
