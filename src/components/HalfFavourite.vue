<template>
  <div></div>
</template>
<script>
import { mapGetters } from "vuex";
import { mapMutations } from "vuex";
export default {
  name: "HalfFavourite",
  props: {
    FavIndex: {
      // array of indices of the tiles
      type: Array,
      required: true,
    },
    //großes Rechteck
    x1: { type: Number }, //oben links
    y1: { type: Number }, //oben link
    x2: { type: Number }, //unten rechts
    y2: { type: Number }, //unten rechts
    sizex: { type: Number }, //Anzahl von Kacheln in einer Reihe
    sizey: { type: Number }, //Anzahl von Kacheln in einer Spalte
    position: { type: String }, //z.B.left,rigth,top, bottom
    mode: { type: Number },
    arrow: { type: String }, //z.B.left,rigth,top, bottom
  },

  data() {
    return {
      xMin: 0, //getBoundingClientRect.leftKoordinaten von der Hälfte, die ändern sich ständig
      xMax: 0, //.right
      yMin: 0, //.top
      yMax: 0, //.bottom
      counter: 0,
      arrowDirection: "",
    };
  },
  computed: {
    ...mapGetters([
      "getCurrentCoor",
      "getLastMouseForRelative",
      "getArrowDirection",
    ]),
  },
  watch: {
    //handelt highlighted für Pfeiltasten
    arrow(value) {
      if (this.mode == 3 && value == this.position) {
        this.setHighlighted(this.FavIndex);
        this.$emit("highlighted", this.FavIndex);
      }
    },
    //achtet auf Favindex, welche Kacheln zugewiesen sind
    FavIndex(arrIndex) {
      if (this.mode == 3) return;

      // console.log("this arrIndex", arrIndex);
      if (arrIndex.length > 0) {
        //arrIndex[0]=ist die Stelle des Index von der gewählten Kachel
        let index = arrIndex[0] % this.sizex; //0,1,2,3
        this.xMin = this.x1 + (index * (this.x2 - this.x1)) / this.sizex; //Hälfte vom großen Rechteck(blau)=thisx2-this.x1
        index = parseInt(arrIndex[0] / this.sizex);
        this.yMin = this.y1 + (index * (this.y2 - this.y1)) / this.sizey; //Hälfte bestimmen
        let lastIndex = arrIndex.length - 1; //arrIndex.length= Hälfte von Länge,max4
        //xMax:rechterRand
        index = (arrIndex[lastIndex] % this.sizex) + 1;
        if (arrIndex.length == 3) {
          // shape is not rectangle
          index++; //rücke noch eins nach rechts, wenn 3Kacheln in einer Hälfte(L)
        }
        this.xMax = this.x1 + (index * (this.x2 - this.x1)) / this.sizex;
        index = parseInt(arrIndex[lastIndex] / this.sizex) + 1;
        this.yMax = this.y1 + (index * (this.y2 - this.y1)) / this.sizey;
      } else {
        this.xMin = 0;
        this.xMax = 0;
        this.yMin = 0;
        this.yMax = 0;
      }
      //liegt die Maus innerhalb vom Rechteck oder nicht?
      if (this.mode == "1") {
        //absolute   mode
        if (
          this.getCurrentCoor.x < this.xMax &&
          this.getCurrentCoor.x > this.xMin &&
          this.getCurrentCoor.y < this.yMax &&
          this.getCurrentCoor.y > this.yMin
        ) {
          this.setHighlighted(arrIndex);
          //emit schickt an parent
          this.$emit("highlighted", arrIndex);
        }
      } else {
        //relative mode
        //wenn leer, dann speichere die coor
        if (
          this.getLastMouseForRelative.x == 0 &&
          this.getLastMouseForRelative.y == 0
        ) {
          //at the beginning save positions
          this.setLastMouseForRelative({
            x: this.getCurrentCoor.x,
            y: this.getCurrentCoor.y,
          });
        }
        //warte auf 30 mouseBewegungen/events
        if (this.counter >= 30) {
          this.counter = 0;
          let direction = "";
          if (
            //Differenz von 2 Positionen,vorher,nachher
            Math.abs(this.getCurrentCoor.x - this.getLastMouseForRelative.x) >
            Math.abs(this.getCurrentCoor.y - this.getLastMouseForRelative.y)
          ) {
            direction = "left";
            if (this.getCurrentCoor.x > this.getLastMouseForRelative.x) {
              direction = "right";
            }
          } else if (
            Math.abs(this.getCurrentCoor.x - this.getLastMouseForRelative.x) <
            Math.abs(this.getCurrentCoor.y - this.getLastMouseForRelative.y)
          ) {
            direction = "top";
            if (this.getCurrentCoor.y > this.getLastMouseForRelative.y) {
              direction = "bottom";
            }
          }
          if (direction == this.position) {
            //highlight me. i have been chosen
            this.setHighlighted(arrIndex);
            this.$emit("highlighted", arrIndex);
          }
          /*console.log(this.getCurrentCoor.x + ", " + this.getCurrentCoor.y);
              console.log(this.getLastMouseForRelative.x + ", " + this.getLastMouseForRelative.y);
              console.log(direction);*/
          //Startschuss für die nächste Runde
          if (this.position == "right" || this.position == "bottom") {
            //Only for half number 2.
            this.setLastMouseForRelative({
              x: this.getCurrentCoor.x,
              y: this.getCurrentCoor.y,
            });
          }
        } else {
          this.counter++;
        }
      }
    },
  },
  methods: {
    ...mapMutations(["setHighlighted", "setLastMouseForRelative"]),
  },
  mounted() {
    //console.log(this.FavIndex);
  },
};
</script>

<style></style>
