<template>
  <div :style="containerStyle" :class="className" @scroll="scrollEvent($event)">
    <div :style="contentStyle"></div>
    <div :style="currentStyle">
      <div ref="row_position" :key="idx" v-for="(i, idx) in visibleData">
        <slot name="item" v-bind="i"></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "virtual_variableSizeList",
  props: {
    //所有列表数据
    data: {
      type: Array,
      default: () => [],
    },
    // 容器类名
    className: {
      type: String,
      default: "",
    },
    // 容器高度
    height: {
      type: [Number, String],
      default: 0,
    },
    // 容器宽度
    width: {
      type: [Number, String],
      default: 0,
    },
    // 每一行预估高度
    estimateItemHeight: {
      type: [Number, String],
      default: 100,
    },
  },
  computed: {
    visibleCount() {
      return Math.ceil(this.screenHeight / this.estimateItemHeight);
    },
    // 当前视图中数据
    visibleData() {
      return this.data.slice(this.startIndex, this.endIndex);
    },
    // 父容器样式
    containerStyle() {
      return {
        overflow: "auto",
        position: "relative",
        willChange: "transform",
        height: `${this.height}px`,
        width: `${this.width}px`,
      };
    },
    // 内容样式
    contentStyle() {
      return {
        position: "absolute",
        left: 0,
        top: 0,
        right: 0,
        zIndex: -1,
        height: `${this.contentHeight}px`,
      };
    },
    // 当前视图样式
    currentStyle() {
      return {
        position: "absolute",
        left: 0,
        right: 0,
        top: 0,
        transform: `translate3d(0,${this.offset}px,0)`,
      };
    },
  },
  created() {
    this.initPositions();
  },
  mounted() {
    this.screenHeight = this.height;
    this.startIndex = 0;
    this.endIndex = this.startIndex + this.visibleCount;
  },
  updated() {
    this.$nextTick(function () {
      if (!this.visibleData || this.visibleData.length === 0) return;
      //获取真实元素大小，修改对应的尺寸缓存
      this.updateItemsSize();
      //更新列表总高度
      let height = this.positions[this.positions.length - 1].bottom;
      this.contentHeight = height;
      //更新真实偏移量
      this.setStartOffset();
    });
  },
  data() {
    return {
      //可视区域高度
      screenHeight: 0,
      //起始索引
      startIndex: 0,
      //结束索引
      endIndex: 0,
      offset: 0, // 偏移量
      contentHeight: 0,
    };
  },
  methods: {
    initPositions() {
      this.positions = this.data.map((d, index) => ({
        index,
        height: this.estimateItemHeight,
        top: index * this.estimateItemHeight,
        bottom: (index + 1) * this.estimateItemHeight,
      }));
    },
    //获取列表起始索引
    getStartIndex(scrollTop = 0) {
      //二分法查找
      return this.binarySearch(this.positions, scrollTop);
    },
    //二分法查找
    binarySearch(list, value) {
      let start = 0;
      let end = list.length - 1;
      let tempIndex = null;
      while (start <= end) {
        let midIndex = parseInt((start + end) / 2);
        let midValue = list[midIndex].bottom;
        if (midValue === value) {
          return midIndex + 1;
        } else if (midValue < value) {
          start = midIndex + 1;
        } else if (midValue > value) {
          if (tempIndex === null || tempIndex > midIndex) {
            tempIndex = midIndex;
          }
          end = end - 1;
        }
      }
      return tempIndex;
    },
    // 更新当前视图中每一项的实际高度和位置
    updateItemsSize() {
      let nodes = this.$refs.row_position;
      nodes.forEach((node) => {
        let rect = node.getBoundingClientRect();
        let height = rect.height;
        let index = +node.id.slice(1);
        let oldHeight = this.positions[index].height;
        let dValue = oldHeight - height;
        //存在差值
        if (dValue) {
          this.positions[index].bottom = this.positions[index].bottom - dValue;
          this.positions[index].height = height;

          for (let k = index + 1; k < this.positions.length; k++) {
            this.positions[k].top = this.positions[k - 1].bottom;
            this.positions[k].bottom = this.positions[k].bottom - dValue;
          }
        }
      });
    },
    //获取当前的偏移量
    setStartOffset() {
      let startOffset =
        this.startIndex >= 1 ? this.positions[this.startIndex - 1].bottom : 0;
      this.offset = startOffset;
    },
    //滚动事件
    scrollEvent(event) {
      const { scrollTop } = event.currentTarget;
      this.startIndex = this.getStartIndex(scrollTop); // 开始索引
      this.endIndex = this.startIndex + this.visibleCount; // 结束索引
      //此时的偏移量
      this.setStartOffset();
    },
  },
};
</script>
