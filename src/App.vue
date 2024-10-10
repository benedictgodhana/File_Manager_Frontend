<template>
  <v-app>
    <!-- <v-navigation-drawer app permanent>
      <Sidebar />
    </v-navigation-drawer> -->

    <v-main style="overflow: hidden; height: 100vh;">
      <v-container fluid>
        <router-view />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Sidebar from '@/components/Sidebar.vue';

export default {
  components: {
    Sidebar,
  },
};
</script>

<style scoped>
.v-main {
  overflow: hidden; /* Prevent scrolling */
  height: 100vh; /* Full height of the viewport */
}
</style>
