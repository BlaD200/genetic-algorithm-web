<template>
    <div id="app">
        <header>
            <b-navbar toggleable="lg" type="dark" variant="dark" fixed="top">
                <b-navbar-brand class="mx-auto" href="#">Timetable generation algorithm</b-navbar-brand>
            </b-navbar>
        </header>
        <b-container class="pt-4 mt-5">
            <genetic-algorithm class="mx-auto"></genetic-algorithm>
        </b-container>
    </div>
</template>

<script>
    // import HelloWorld from './components/HelloWorld.vue'
    import GeneticAlgorithm from "@/components/GeneticAlgorithm";

    export default {
        name: 'App',
        components: {
            GeneticAlgorithm
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }
</style>
