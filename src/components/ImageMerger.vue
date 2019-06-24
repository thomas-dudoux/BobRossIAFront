<template>
    <v-form enctype="multipart/form-data" autocomplete="on" @submit.prevent>
        <v-container grid-list-md>
            <v-layout row wrap>
                <v-flex md6 sm12>
                    <v-card hover class="pa-2 ma-2 deep-purple darken-4 transparent">
                        <v-layout align-center column>
                            <v-card-title class="display-1 font-italic">Image source</v-card-title>
                            <v-flex v-if="sourceImageShower">
                                <v-img :src="sourceImageShower" height="256" width="256"></v-img>
                            </v-flex>
                            <v-flex class="mt-2">
                                <input type="file" name="sourceImage" @change="onImageLoad" required="required">
                            </v-flex>
                        </v-layout>
                    </v-card>
                </v-flex>
                <v-flex md6 sm12>
                    <v-card hover class="pa-2 ma-2 indigo darken-4 transparent">
                        <v-layout align-center column>
                            <v-card-title class="display-1 font-italic">Image de style</v-card-title>
                            <v-flex v-if="styleImageShower">
                                <v-img :src="styleImageShower" height="256" width="256"></v-img>
                            </v-flex>
                            <v-flex class="mt-2">
                                <input type="file" name="styleImage" @change="onImageLoad" required="required">
                            </v-flex>
                        </v-layout>
                    </v-card>
                </v-flex>
            </v-layout>
        </v-container>
    </v-form>
</template>

<script lang="ts">
import Vue from 'vue'
import { Component } from 'vue-property-decorator'
import { HTMLInputEvent } from '@/utils.ts'


@Component
export default class ImageMerger extends Vue {

    sourceImage: File | null = null
    styleImage: File | null = null
    sourceImageShower  = ''
    styleImageShower = ''

    onImageLoad (event?: HTMLInputEvent) : void {
        if (event != null && event.target != null && event.target.files != null) {
            let fr = new FileReader()
            if (event.target.name === "styleImage") {
                this.styleImage = event.target.files[0]
                // @ts-ignore
                fr.onload = e => this.styleImageShower = e.target.result
            } 
            if (event.target.name === "sourceImage") {
                this.sourceImage = event.target.files[0]
                // @ts-ignore
                fr.onload = e => this.sourceImageShower = e.target.result
            }
            fr.readAsDataURL(event.target.files[0])
        }
    }

    retrieveForm () : {sourceImage: File, styleImage: File} | null  {
        if (this.sourceImage && this.styleImage)
            return {
                sourceImage: this.sourceImage,
                styleImage: this.styleImage
            }
        return null
    }

}
</script>

