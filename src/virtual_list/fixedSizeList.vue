<template>
  <div :class="className" :style="containerStyle" @scroll="scrollEvent($event)">
    <div :style="{ ...contentStyle, height: `${listHeight}px` }">
      <div :style="{ ...currentStyle, transform: getTransform }">
        <div
          v-for="(i, idx) in visibleData"
          :key="idx"
          :style="{ height: itemHeight + 'px' }"
        >
          <slot name="item" v-bind="i"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "virtual_fixedSizeList",
  props: {
    // 所有数据
    data: {
      type: Array,
      default: () => [],
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
    // 容器类名
    className: {
      type: String,
      default: "",
    },
    // 每项高度
    itemHeight: {
      type: [Number, String],
      default: 0,
    },
  },
  computed: {
    // 内容列表总高度
    listHeight() {
      return this.data.length * this.itemHeight;
    },
    //可显示的列表项数
    visibleCount() {
      return Math.ceil(this.height / this.itemHeight);
    },
    //偏移量对应的style
    getTransform() {
      return `translate3d(0,${this.offsetTop}px,0)`;
    },
    //获取真实显示列表数据
    visibleData() {
      return this.data.slice(this.start, Math.min(this.end, this.data.length));
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
      };
    },
    // 当前视图样式
    currentStyle() {
      return {
        position: "absolute",
        left: 0,
        right: 0,
        top: 0,
      };
    },
  },
  mounted() {
    this.start = 0; // 开始索引
    this.end = this.start + this.visibleCount; // 结束索引
  },
  data() {
    return {
      offsetTop: 0, // 偏移量
      start: 0, // 开始索引
      end: null, // 结束索引
    };
  },
  methods: {
    scrollEvent(event) {
      const { scrollTop } = event.currentTarget;
      this.start = Math.floor(scrollTop / this.itemHeight); // 开始索引
      this.end = this.start + this.visibleCount; // 结束索引
      this.offsetTop = scrollTop - (scrollTop % this.itemHeight); // 卷曲高度
    },
  },
};
</script>
