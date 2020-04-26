<template>
  <div>
    <Transfer>1111</Transfer>
  </div>
</template>

<script>
import { Transfer } from "view-design";
import "view-design/dist/styles/iview.css";

export default {
  name: "index",
  components: {
    Transfer
  },
  data() {
    return {
      data1: this.getMockData(),
      targetKeys1: this.getTargetKeys()
    };
  },
  methods: {
    getMockData() {
      let mockData = [];
      for (let i = 1; i <= 20; i++) {
        mockData.push({
          key: i.toString(),
          label: "Content " + i,
          description: "The desc of content  " + i,
          disabled: Math.random() * 3 < 1
        });
      }
      return mockData;
    },
    getTargetKeys() {
      return this.getMockData()
        .filter(() => Math.random() * 2 > 1)
        .map(item => item.key);
    },
    render1(item) {
      return item.label;
    },
    handleChange1(newTargetKeys, direction, moveKeys) {
      console.log(newTargetKeys);
      console.log(direction);
      console.log(moveKeys);
      this.targetKeys1 = newTargetKeys;
    }
  }
};
</script>

<style scoped lang="scss">
</style>
