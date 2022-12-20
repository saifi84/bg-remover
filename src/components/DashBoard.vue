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

            //formData.append('previewImage', photo)
            formData.append('image_file', file, file.name)

            // axios.post('https://api.remove.bg/v1.0/removebg',
            //         formData, {
            //             headers: {
            //                 'Content-Type': 'multipart/form-data',
            //                 'X-Api-Key': 'eT4vfE8dvxRMuquT7EEAiNb4'
            //             }
            //         }
            //     ).then(function (response) {
            //         this.bgImage = fs.readFileSync(response.data)
            //         this.bgImage = response.data;
            //         // this.bgImage = response.data;
            //         console.log('SUCCESS!!');
            //     })
            //     .catch(function () {
            //         console.log('FAILURE!!');
            //     });
            axios({
                    method: 'post',
                    url: 'https://api.remove.bg/v1.0/removebg',
                    data: formData,
                    responseType: 'arraybuffer',
                    headers: {
                        // ...formData.getHeaders(),
                        'X-Api-Key': 'eT4vfE8dvxRMuquT7EEAiNb4',
                        'Content-Type': 'multipart/form-data'

                    }
                })
                .then((response) => {
                    if (response.status != 200) return console.error('Error:', response.status, response.statusText);
                    // fs.writeFileSync("no-bg.png", response.data);
                    var base64 = btoa(
                        new Uint8Array(response.data)
                        .reduce((data, byte) => data + String.fromCharCode(byte), '')
                    );
                    // var base64String = btoa(String.fromCharCode.apply(null, new Uint8Array(response.data)));
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
            // const image = `data:image/png;base64,${bgImage}`;
            // const imageBlog = image.blob()
            // const imageURL = URL.createObjectURL(imageBlog)

            // const link = document.createElement('a')
            // link.href = imageURL
            // link.download = 'test.png'
            // document.body.appendChild(link)
            // link.click()
            // document.body.removeChild(link)
            const linkSource = `data:image/png;base64,${bgImage}`;
            const downloadLink = document.createElement("a");
            downloadLink.href = linkSource;
            downloadLink.download = 'test.png';
            downloadLink.click();
            // var FILE = window.URL.createObjectURL(bgImage);

            //     var docUrl = document.createElement('x');
            //     docUrl.href = FILE;
            //     docUrl.setAttribute('download', 'file.pdf');
            //     document.body.appendChild(docUrl);
            //     docUrl.click();

            // var a = document.createElement('a'); //Create <a>
            // a.href = "data:image/png;base64," + bgImage;//Image Base64 Goes here
            // a.download = "Image.png"; //File name Here
            // a.click(); //Downloaded file
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
