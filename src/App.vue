<template>
  <v-app >
    <Navbar />
    <v-content class="app-color">
      <router-view></router-view>
    </v-content>
  </v-app>
</template>

<script>
import Navbar from './components/Navbar.vue'

export default {
  components: { Navbar }
}
</script>

<style>
  .app-color {
    background-color: #DBDFE6;
  }
</style>