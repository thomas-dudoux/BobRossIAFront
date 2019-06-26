<template>
    <v-container>
        <h1 class="display-2 font-weight-bold text-xs-center">Bienvenue dans l'application BobRossIA !</h1>
        <v-divider class="pa-2 ma-2" inset/>
        <image-merger ref="im"></image-merger>
        <form-merger ref="formm"></form-merger>
        <v-layout align-center justify-center column>
            <v-flex xs12>
                <span class="headline">Laisse Bob peindre</span>
                <v-btn fab large color="primary" @click="sendToServer">
                    <v-icon>brush</v-icon>
                </v-btn>
            </v-flex>
            <v-flex xs12 v-if="processing">
                <v-progress-circular :size="80" :width="8" indeterminate color="purple"></v-progress-circular>
            </v-flex>
            <v-flex xs12 v-if="!processing && done">
                <span class="headline">Télécharge ton image</span>
                    <v-btn fab large color="primary" :href="this.styledImages.href" :download="this.styledImages.download">
                        <v-icon>cloud_download</v-icon>
                </v-btn>
            </v-flex>
            <!--<v-flex xs12 v-if="styledImage">
                <v-img :src="styledImageShower" height="256" width="256" contain></v-img>
            </v-flex>-->
        </v-layout>
    </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import { Component } from 'vue-property-decorator'
import FormMerger from '@/components/FormMerger.vue'
import ImageMerger from '@/components/ImageMerger.vue'
import * as $ from 'jquery'
 
@Component({
    components: {
        'image-merger': ImageMerger,
        'form-merger': FormMerger
    },
})
export default class Home extends Vue {

     styledImages = {
         blob: null,
         href: '',
         download: ''
     }
     processing = false
     done = false

    async sendToServer () : Promise<void> {
        // @ts-ignore
        let imgs = this.$refs.im.retrieveForm()
        // @ts-ignore
        let conf = this.$refs.formm.retrieveForm()
        if (imgs == null) alert('Vous devez renseignez les deux images !')
        if (conf == null) alert('Vous devez la configuration du transfert de style !')
        if (imgs) {
            this.processing = true
            let fd = new FormData()
            fd.append('source_image', imgs.sourceImage)
            fd.append('style_image', imgs.styleImage)
            fd.append('model', conf.selectedModel)
            fd.append('num_iterations', conf.numIterations)
            $.ajax(`http://127.0.0.1:5555/style_transfer`, {
                type: 'POST',
                data: fd,
                processData: false,
                contentType: false, // 'multipart/form-data'
                dataType: 'text',
                crossDomain: true,
                success: (data, textStatus, jqXHR) => {
                    let bdata = this._base64ToArrayBuffer(data)
                    let blob = new Blob([bdata], {type: 'octet/stream'})
                    let filePath = window.URL.createObjectURL(blob)
                    // @ts-ignore
                    this.styledImages.blob = blob
                    this.styledImages.href = filePath
                    this.styledImages.download = filePath.substr(filePath.lastIndexOf('/') + 1)  + '.png'
                    this.processing = false
                    this.done = true 
                }
            })
        }
    }

    _base64ToArrayBuffer (bs: string) : Uint8Array {
        let ascii = null
        let binaryString = window.atob(bs)
        let binaryLen = binaryString.length
        let bytes = new Uint8Array(binaryLen)
        for (let i = 0; i < binaryLen; i++) {
            ascii = binaryString.charCodeAt(i)
            bytes[i] = ascii
        }
        return bytes
    }

 }

</script>
