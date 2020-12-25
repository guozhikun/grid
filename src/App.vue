<template>
  <div id="app">
    <div class="wrap">
      <grid :gridList="gridList" @getGridList="getGridList" :draggable ="draggable"></grid>
      <!-- <button @click="startDrag">{{draggable?'endDrag':'startDrag'}} </button> -->
    </div>
  </div>
</template>

<script>
import Grid from "./components/grid/index.vue";
 
export default {
  name: "App",
  components: {
    Grid,
  },
  data() {
    return {
      gridList: [
        { name: "item1", sort: 1 },
        { name: "item2", sort: 2 },
        { name: "item3", sort: 3 },
        { name: "item4", sort: 4 },
        { name: "item5", sort: 5 },
        { name: "item6", sort: 6 },
        { name: "item7", sort: 7 },
        { name: "item8", sort: 8 },
        { name: "item9", sort: 9 },
      ],
      draggable:true
    };
  },
  methods:{
    getGridList(list){
      this.gridList = list
    },
    startDrag(){
      this.draggable = !this.draggable;
    }
  }
};
</script>
<style scoped>
.wrap {
  width: 331px;
  padding: 22px;
  height: 600px;
  border: 1px solid #ccc;
  margin: 0 auto;
  background: #000;
}
</style>
 
