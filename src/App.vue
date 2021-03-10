<template>
  <div id="app">
    <!--WebGazer @onUpdated="onNewData" :off="false" /-->
    <!-- <GazeCloud @update="onUpdate" /> -->

    <Home />
  </div>
</template>
<script>
import Home from "./components/Home.vue";

// import Favorites from "@/components/Favorites.vue";
//import GazeCloud from "@/components/GazeCloud.vue";

import { mapMutations } from "vuex";

export default {
  // components: { Tastatur },
  name: "App",
  //components:
  //{ GazeCloud },
  components: { Home },
  // components: { Favorites },
  data: () => ({
    windowHeight: 0,
    windowWidth: 0,
    currentX: 0,
    currentY: 0,
    x: 0,
    y: 0,
  }),

  created: function() {
    this.windowHeight = window.innerHeight;
    this.windowWidth = window.innerWidth;
    window.addEventListener("mousemove", this.onUpdate);
  },
  destroyed: function() {
    window.removeEventListener("mousemove", this.onUpdate);
  },
  methods: {
    ...mapMutations(["setCurrentCoor"]),
    onUpdate(event) {
      this.currentX = event.clientX;
      this.currentY = event.clientY;
      this.setCurrentCoor({
        x: this.currentX,
        y: this.currentY,
      });
      //console.log(this.currentX, this.currentY);
    },
    onNewData(coord) {
      this.x = coord.x;

      this.y = coord.y;

      console.log("This is X-Value:", this.x);

      console.log("This is Y-Value:", this.y);
    },
  },
};
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
} */
</style>
