<template>
  <div :class="className" :style="style" @mousedown="mousedown">
    <slot></slot>
  </div>
</template>
<script>
export default {
  data() {
    return {
      animate: true,
      timer: null,
      dragging: false,
      mouseMoveStartX: 0,
      mouseMoveStartY: 0,
      shiftStartX: 0,
      shiftStartY: 0,
      shiftEndX: 0,
      shiftEndY: 0,
    };
  },
  props: {
    dance: {
      type: Boolean,
      default: false,
    },
    draggable: {
      type: Boolean,
      default: true,
    },
    itemMg: {
      type: Number,
      default: 30,
    },
    sort: {
      type: Number,
      default: -1,
    },
    height: {
      type: Number,
      default: 60,
    },
    width: {
      type: Number,
      default: 60,
    },
  },
  computed: {
    className() {
      let { dragging, animate, draggable, dance } = this;
      return [
        "grid-item-wrap",
        {
          "grid-item-dance": draggable && dance,
          "grid-item-animate": animate,
        },
      ];
    },
    rowCount() {
      return 4;
    },
    left() {
      return this.dragging
        ? this.shiftEndX
        : ((this.sort - 1) % this.rowCount) * (this.width + this.itemMg);
    },
    top() {
      return this.dragging
        ? this.shiftEndY
        : Math.floor((this.sort - 1) / this.rowCount) *
            (this.height + this.itemMg);
    },
    style() {
      let { top, left } = this;
      return {
        height: this.height + "px",
        width: this.width + "px",
        transform: `translate3d(${left}px, ${top}px, 0)`,
      };
    },
  },
  methods: {
    mousedown(event) {
      this.timer = setTimeout(() => {
        this.dragStart(event);
      }, 100);
      document.addEventListener("mouseup", this.documentMouseUp);
      document.addEventListener("touchend", this.documentMouseUp);
    },
    dragStart(event) {
      let e = event.touches ? event.touches[0] : event;
      this.mouseMoveStartX = e.pageX;
      this.mouseMoveStartY = e.pageY;
      this.shiftEndX = this.shiftStartX = this.left;
      this.shiftEndY = this.shiftStartY = this.top;
      this.dragging = true;
      this.animate = false;
      document.addEventListener("mousemove", this.documentMouseMove);
      document.addEventListener("touchmove", this.documentMouseMove);
    },
    documentMouseMove(event) {
      if (this.draggable && this.dragging) {
        this.drag(event);
      }
    },
    drag(event) {
      let e = event.touches ? event.touches[0] : event;
      let distanceX = e.pageX - this.mouseMoveStartX;
      let distanceY = e.pageY - this.mouseMoveStartY;
      this.shiftEndX = distanceX + this.shiftStartX;
      this.shiftEndY = distanceY + this.shiftStartY;
      let gridX = Math.round(this.shiftEndX / (this.width + this.itemMg));
      let gridY = Math.round(this.shiftEndY / (this.width + this.itemMg));
      console.log(gridX);
      gridX = Math.min(gridX, this.rowCount - 1); //拖动时所在列
      gridY = Math.max(gridY, 0); //拖动时所在行
      let gridPosition = gridX + gridY * this.rowCount; //拖动时在数组的序号
      console.log(gridPosition);
      let $event = {
        gridPosition,
        gridX,
        gridY,
        sort: this.sort,
      };
      this.$emit("drag", $event);
    },
    documentMouseUp(event) {
      if (this.timer) {
        clearTimeout(this.timer);
        this.timer = null;
      }
      this.animate = true;
      this.dragging = false;
      this.mouseMoveStartX = 0;
      this.mouseMoveStartY = 0;
      this.shiftStartX = 0;
      this.shiftStartY = 0;
      document.removeEventListener("mousemove", this.documentMouseMove);
      document.removeEventListener("touchmove", this.documentMouseMove);
      document.removeEventListener("mouseup", this.documentMouseUp);
      document.removeEventListener("touchend", this.documentMouseUp);
    },
  },
};
</script>
<style scoped>
.grid-item-wrap {
  position: absolute;
  background: #fff;
  cursor: pointer;
  border-radius: 10px;
  text-align: center;
  line-height: 60px;
}

/* .grid-item-dance {
   animation: dance 2s 0s infinite; 
} */

.grid-item-animate {
  transition: transform 500ms ease;
}
</style>