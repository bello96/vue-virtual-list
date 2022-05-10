> #### 使用说明

- 基于 vue 框架用于实现**不定内容宽高**、**高性能不卡顿渲染长列表数据（虚拟列表）** 的解决方案
- 同时兼容 vue2 和 vue3

> #### 安装引入

```
1. npm install vue-virtual-list
2. import { FixedSizeList, VariableSizeList } from 'vue-virtual-list'
```

> #### 使用示例

```javascript
<template>
  <h3>高度固定</h3>
  <fixed-size-list
    className="fixedClass"
    :data="fixedData"
    width="400"
    height="600"
    itemHeight="80"
  >
    <template #item="{ value, index }">
      <div class="itemClass">
        <span>{{ index }}</span>
        <span>{{ value }}</span>
      </div>
    </template>
  </fixed-size-list>
  
  <h3>高度自适应</h3>
  <variableSizeList
    className="variableClass"
    :data="variableData"
    width="400"
    height="600"
    estimateItemHeight="50"
  >
    <template #item="{ value, index }">
      <div class="itemClass">
        <span>{{ index }}</span>
        <span>{{ value }}</span>
      </div>
    </template>
  </variableSizeList>
</template>

<script>
export default {
  data() {
    return {
      fixedData,
      variableData,
    };
  }
}
</script>
```

> #### 参数说明

- **className** : 类名 _（默认：无）_

- **data** : 需要展示的所有数据 _（默认：[]）_

- **width** : 指定宽度值 _（默认：0，接收字符串数字和纯数字）_

- **height** : 指定高度值 _（默认：0，接收字符串数字和纯数字）_

- **itemHeight** : 每一项的高度 _（默认：0，仅在 FixedSizeList 中生效）_

- **estimateItemHeight** : 每一项的预估高度 _（默认：100，仅在 variableSizeList 中生效）_
