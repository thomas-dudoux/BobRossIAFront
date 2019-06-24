<template>
    <v-app id="app" dark>
        <v-content>
            <router-view></router-view>
        </v-content>
    </v-app>
</template>

<script>
export default {
    name: 'front_bob_ross'
}
</script>

<style scoped>
    #app {
        background-image: url("~@/assets/bob_ross_galaxy.jpg");
    }
</style>
