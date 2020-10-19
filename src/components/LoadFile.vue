<template>
    <div class="container">
        <div class="large-12 medium-12 small-12 cell">
            <label>File
                <input type="file" id="file" ref="file" v-on:change="handleFileUpload()"/>
            </label>
            <button v-on:click="submitFile()">Submit</button>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    const axiosInstance = axios.create({
        baseURL: 'http://localhost:9000/',
        timeout: 1000,
        headers: {'X-Custom-Header': 'foobar'}
    });

    export default {
        name: "load-file",
        data() {
            return {
                file: ""
            }
        },
        methods: {
            submitFile() {
                /*
                        Initialize the form data
                    */
                let formData = new FormData();

                /*
                    Add the form data we need to submit
                */
                formData.append('file', this.file);

                /*
                  Make the request to the POST /single-file URL
                */
                axiosInstance.post('load/json/all',
                    formData,
                    {
                        headers: {
                            'Content-Type': 'multipart/form-data'
                        }
                    }
                ).then(function () {
                    console.log('SUCCESS!!');
                }).catch(function () {
                    console.log('FAILURE!!');
                });
            },

            /*
              Handles a change on the file upload
            */
            handleFileUpload() {
                this.file = this.$refs.file.files[0];
            }
        }
    }
</script>

<style scoped>

</style>