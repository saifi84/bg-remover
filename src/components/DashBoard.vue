<template>
<div id="app">
    <v-app>
        <v-card>
            <v-tabs v-model="tab" background-color="deep-purple accent-4" centered dark icons-and-text>
                <v-tabs-slider></v-tabs-slider>
                <v-tab href="#subscribe">
                    ORIGNAL
                </v-tab>

                <v-tab href="#contact">
                    BGREMOVE
                </v-tab>
            </v-tabs>

            <v-tabs-items v-model="tab">
                <v-tab-item :key="1" value="subscribe">
                    <v-card flat>
                        <div class="imagePreviewWrapper mt-10 d-flex flex-column align-center" :style="{ 'background-image': `url(${previewImage})` }" @click="selectImage">
                        </div>
                        <div class="d-flex flex-column align-center pb-4">
                            <input ref="fileInput" type="file" @input="pickFile">
                        </div>
                    </v-card>
                </v-tab-item>
                <v-tab-item :key="2" value="contact">
                    <v-card flat>
                        <div class="mt-10 d-flex flex-column align-center">
                            <a id="a">
                                <img :src="`data:image/png;base64,${bgImage}`" />
                            </a>
                        </div>
                        <div class="d-flex flex-column align-center">
                            <v-btn class="ma-2 mt-10 d-flex flex-column align-center" color="info" @click="downloadImage">
                            Download
                            <template v-slot:loader>
                                <span class="custom-loader">
                                    <v-icon light>mdi-cached</v-icon>
                                </span>
                            </template>
                        </v-btn>
                        </div>
                       
                    </v-card>
                </v-tab-item>
            </v-tabs-items>
        </v-card>
    </v-app>
</div>
</template>

<script>
import FormData from 'form-data';
import axios from 'axios';
// import fs from "fs";

export default {
    name: 'DashBoard',
    data() {
        return {
            tab: "subscribe",
            previewImage: null,
            bgImage: null,
            item: {
                image: null,
                imageUrl: null
            }
        }
    },

    methods: {
        selectImage() {
            this.$refs.fileInput.click()
        },

        RemoveBg(file) {
            const formData = new FormData();
            formData.append('size', 'auto');
            formData.append('image_file', file, file.name)
            axios({
                    method: 'post',
                    url: 'https://api.remove.bg/v1.0/removebg',
                    data: formData,
                    responseType: 'arraybuffer',
                    headers: {
                        'X-Api-Key': 'eT4vfE8dvxRMuquT7EEAiNb4',
                        'Content-Type': 'multipart/form-data'

                    }
                })
                .then((response) => {
                    if (response.status != 200) return console.error('Error:', response.status, response.statusText);
                  
                    var base64 = btoa(
                        new Uint8Array(response.data)
                        .reduce((data, byte) => data + String.fromCharCode(byte), '')
                    );
            
                    this.bgImage = base64;
                })
                .catch((error) => {
                    return console.error('Request failed:', error);
                });
        },
        pickFile() {
            let input = this.$refs.fileInput
            let file = input.files
            if (file && file[0]) {
                let reader = new FileReader
                reader.onload = e => {
                    this.previewImage = e.target.result
                    this.RemoveBg(file[0])
                }
                reader.readAsDataURL(file[0])
                this.$emit('input', file[0])
            }
        },
        downloadImage(bgImage) {
            const linkSource = `data:image/png;base64,${bgImage}`;
            const downloadLink = document.createElement("a");
            downloadLink.href = linkSource;
            downloadLink.download = 'test.png';
            downloadLink.click();
        }
    }
}
</script>

<style lang="scss" scoped>
.imagePreviewWrapper {
    width: 250px;
    height: 250px;
    display: block;
    cursor: pointer;
    margin: 0 auto 30px;
    background-size: cover;
    background-position: center center;
}
</style>
