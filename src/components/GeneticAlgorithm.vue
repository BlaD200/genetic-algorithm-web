<template>
    <div>
        <file-pond
                id="file-upload"
                name="file"
                ref="pond"
                :allow-multiple="true"
                :max-files="5"
                accepted-file-types="application/json"
                server="http://localhost:9000/load/json/all"
                v-bind:files="files"
                v-on:init="handleFilePondInit"
        />
    </div>
</template>

<script>
    // Import Vue FilePond
    import vueFilePond from 'vue-filepond';

    // Import FilePond styles
    import 'filepond/dist/filepond.min.css';

    // Import FilePond plugins
    // Please note that you need to install these plugins separately

    // Import image preview and file type validation plugins
    import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type';

    // Create component
    const FilePond = vueFilePond(FilePondPluginFileValidateType);

    // import LoadFile from "@/components/LoadFile";
    export default {
        name: "genetic-algorithm",
        components: {
            FilePond
        },
        data() {
            return {
                files: []
            }
        },
        methods: {
            handleFilePondInit: function() {
                console.log('FilePond has initialized');

                // FilePond instance methods are available on `this.$refs.pond`
            }
        }
    }
</script>

<style scoped>
    div {
        width: 400px;
    }
    p {
        text-align: center;
    }
</style>