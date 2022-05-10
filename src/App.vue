<template>
  <div id="app">
    <div class="app_item">
      <h3>高度固定</h3>
      <fixed-size-list className="fixedClass" :data="fixedData" width="400" height="600" itemHeight="80">
        <template #item="{ value, index }">
          <div :class="['itemClass', index % 2 ? 'listItemOdd' : 'listItemEven']">
            <span>{{ index }}</span>
            <span>{{ value }}</span>
          </div>
        </template>
      </fixed-size-list>
    </div>

    <div class="app_item">
      <h3>高度自适应</h3>
      <variableSizeList className="variableClass" :data="variableData" width="400" height="600" estimateItemHeight="50">
        <template #item="{ value, index }">
          <div :class="['itemClass', index % 2 ? 'listItemOdd' : 'listItemEven']">
            <span>{{ index }}</span>
            <span>{{ value }}</span>
          </div>
        </template>
      </variableSizeList>
    </div>
  </div>
</template>

<script>
import faker from "faker";

import { FixedSizeList, VariableSizeList } from "./virtual_list";

let variableData = [];
let fixedData = [];
for (let index = 0; index < 2000; index++) {
  fixedData.push({
    index,
    value: faker.internet.email(),
  });
  variableData.push({
    index,
    value: faker.lorem.sentences(), // 长文本
  });
}

export default {
  name: "App",
  data() {
    return {
      fixedData,
      variableData,
    };
  },
  components: {
    FixedSizeList,
    VariableSizeList,
  },
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
}

.app_item {
  text-align: center;
  padding: 20px;
}

.fixedClass,
.variableClass {
  border: 1px solid #bbb;
  border-radius: 3px;
}

.itemClass {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.listItemOdd {
  background-color: #90ee90;
}

.listItemEven {
  background-color: #f08080;
}
</style>
