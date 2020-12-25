<template>
  <div class="grid-wrap" :style="style">
    <grid-item
      v-for="(item, index) in gridlist"
      :key="index"
      :sort="item.sort"
      @drag="onDrag"
      :draggable="draggable"
      >{{ item.name }}</grid-item
    >
  </div>
</template>
<script>
import GridItem from "../../components/gridItem/index.vue";
export default {
  name: "Grid",
  components: {
    GridItem,
  },

  props: {
    draggable: {
      type: Boolean,
      default: false,
    },
    gridList: {
      type: Array,
      default: () => [],
    },
  },
  computed: {
    height() {
      return 280;
    },
    style() {
      return {
        height: this.height + "px",
      };
    },
  },
  data() {
    return {
      gridlist: this.gridList,
    };
  },
  methods: {
    onDrag(event) {
      this.sortList(event.sort, event.gridPosition + 1);
    },
    sortList(index, gridEnd) {
      gridEnd = Math.max(gridEnd, 0);
      gridEnd = Math.min(gridEnd, this.gridlist.length);
      let targetItem = this.gridlist.find((item) => item.sort === index);
      this.gridlist = this.gridlist.map((item) => {
        if (item.sort === targetItem.sort) {
          return {
            ...item,
            sort: gridEnd,
          };
        }
        if (item.sort > index && gridEnd >= item.sort) {
          return {
            ...item,
            sort: item.sort - 1,
          };
        }

        if (item.sort < index && gridEnd <= item.sort) {
          return {
            ...item,
            sort: item.sort + 1,
          };
        }
        return item;
      });
      this.$emit("getGridList", this.gridlist);
    },
  },
};
</script>