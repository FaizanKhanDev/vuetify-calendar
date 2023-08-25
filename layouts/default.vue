<template>
  <v-app>
    <v-toolbar color="orange" elevation="7" max-height="50px">
      <v-btn to="/" class="mb-3">Type Weekly Calendar </v-btn>
      <v-btn to="/draganddropcalendar" class="mb-3 ml-4">Drag And Drop Calendar</v-btn>
    </v-toolbar>
    <nuxt />
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js',
      drawer: false,
      group: null,
    }
  }
}
</script>
