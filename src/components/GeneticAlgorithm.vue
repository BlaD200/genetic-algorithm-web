<template>
    <b-row>
        <b-col></b-col>
        <b-col cols="12" md="6">
            <file-pond
                    id="file-upload"
                    name="file"
                    ref="pond"
                    :allow-multiple="true"
                    :max-files="5"
                    accepted-file-types="application/json"
                    server="http://localhost:9000/load/json/all"
                    v-bind:files="myFiles"
                    v-on:init="handleFilePond"
                    v-on:addfile="handleFilePond"
            />

            <hr>

            <b-button class="p-2" block v-b-toggle.accordion-1 variant="outline-info">Settings</b-button>
            <b-collapse id="accordion-1" accordion="my-accordion" role="tabpanel">
                <b-card-body>
                    <b-row>
                        <b-col>
                            <!--suppress XmlInvalidId -->
                            <label for="unit-count-inp">Choose the number of population:</label>
                            <b-form-input id="unit-count-inp" v-model="units"
                                          type="range" min="20" max="200"
                            ></b-form-input>
                            <b-form-spinbutton id="unit-count-spin" v-model="units"
                                               min="20" max="200">

                            </b-form-spinbutton>
                        </b-col>
                    </b-row>
                    <hr>
                    <b-row>
                        <b-col>
                            <!--suppress XmlInvalidId -->
                            <label for="iterations-inp">Choose the number of iterations:</label>
                            <b-form-input id="iterations-inp" v-model="iterations"
                                          type="range" min="50" max="1000"
                            ></b-form-input>
                            <b-form-spinbutton id="iterations-spin" v-model="iterations"
                                               min="50" max="1000"
                            ></b-form-spinbutton>
                        </b-col>
                    </b-row>
                    <hr>
                    <b-row>
                        <b-col>
                            <!--suppress XmlInvalidId -->
                            <label for="mutation-rate-inp">Choose the mutation rate:</label>
                            <b-form-input id="mutation-rate-inp" v-model="mutationRate" type="range" min="0" max="0.1"
                                          step="0.005"></b-form-input>
                            <b-form-spinbutton id="mutation-rate-spin" v-model="mutationRate" min="0" max="0.1"
                                               step="0.005"></b-form-spinbutton>
                        </b-col>
                    </b-row>
                </b-card-body>
            </b-collapse>

            <hr>

            <b-row v-if="!loading">
                <b-col></b-col>
                <b-col cols="12" md="6" class="text-center my-2">
                    <b-button
                            variant="outline-success"
                            :disabled="myFiles.length === 0"
                            @click="generateTimetable"
                            v-b-tooltip.hover.top
                            :title="myFiles.length === 0 ? 'Upload file/s first' : ''"
                    >Generate timetable
                    </b-button>
                </b-col>
                <b-col></b-col>
            </b-row>
            <b-row v-else>
                <b-col></b-col>
                <b-col cols="6" class="text-center mx-5 my-2">
                    <b-spinner style="width: 3rem; height: 3rem;" label="Large Spinner"></b-spinner>
                </b-col>
                <b-col></b-col>
            </b-row>
        </b-col>
        <b-col></b-col>


    </b-row>
</template>

<script>
    // Import Vue FilePond
    import vueFilePond from 'vue-filepond';
    // Import FilePond styles
    import 'filepond/dist/filepond.min.css';
    // Import image preview and file type validation plugins
    import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type';
    // Create component
    const FilePond = vueFilePond(FilePondPluginFileValidateType);

    const axios = require('axios');
    const instance = axios.create({
        baseURL: 'http://localhost:9000/algorithm/',
        timeout: 1000
    });

    const FileSaver = require('file-saver');

    export default {
        name: "genetic-algorithm",
        components: {
            FilePond
        },
        data() {
            return {
                myFiles: [],
                units: 10,
                iterations: 100,
                mutationRate: 0.05,
                loading: false
            }
        },
        methods: {
            handleFilePond: function () {
                console.log('FilePond has initialized');

                // FilePond instance methods are available on `this.$refs.pond`
                this.myFiles = this.$refs.pond.getFiles()
            },
            generateTimetable() {
                this.loading = true;
                instance.get("/start", {
                    params: {
                        unitsNumber: this.units,
                        iterations: this.iterations,
                        mutationRate: this.mutationRate
                    },
                    responseType: 'blob'
                }).then(response => {
                    if (response.headers['correct'] === "true")
                        this.downloadTimetable(response.data)
                    else {
                        const h = this.$createElement
                        const messageVNode = h('div', [
                            h('p', [
                                'This could happen because of some incorrect data provided, ' +
                                'or because of setting. ' +
                                'Try to review your data and/or change population/iterations/mutations to bigger size. '
                            ]),
                            h('p', [
                                'Despite of message bellow, you still could download generated timetable ' +
                                'and fix some problems by hand. '
                            ]),
                            h('p',[
                                h('b', [
                                    'Do you want to download it?'
                                ])
                            ])
                        ])
                        this.$bvModal.msgBoxConfirm([messageVNode], {
                                title: "Cannot generate correct timetable",
                                size: 'md',
                                buttonSize: 'sm',
                                okVariant: 'outline-success',
                                cancelVariant: 'outline-secondary',
                                headerClass: 'border-bottom-0',
                                footerClass: 'p-2 border-top-0',
                                centered: true,
                                autoFocusButton: 'ok'
                            }).then(choice => {
                                if (choice)
                                    this.downloadTimetable(response.data)
                        })
                    }
                    this.loading = false;
                }).catch(error => {
                    if (error.response){
                        this.$bvModal.msgBoxOk(
                            "Response code: " + error.response.status +
                            " Message: " + error.response.data, {
                                title: "Error when processing request",
                                size: 'md',
                                buttonSize: 'sm',
                                okVariant: 'outline-info',
                                cancelVariant: 'outline-secondary',
                                headerClass: 'border-bottom-0',
                                footerClass: 'p-2 border-top-0',
                                centered: true,
                                autoFocusButton: 'ok'
                            })
                    }
                    this.loading = false;
                })
            },
            downloadTimetable(data){
                FileSaver.saveAs(data, "TimeTable.xlsx");
            }
        }
    }
</script>

<style scoped>

</style>