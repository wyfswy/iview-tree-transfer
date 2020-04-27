<template>
  <div class="wrapper">
    <tree-transfer :data-source="origin"></tree-transfer>
  </div>
</template>

<script>
import TreeTransfer from "../../src/index.vue";

export default {
  name: "App",
  components: { TreeTransfer },
  data() {
    return {
      origin: [
        {
          title: "parent 1",
          value: "1",
          expand: true,
          children: [
            {
              title: "parent 1-1",
              value: "1-1",
              expand: true,
              children: [
                {
                  title: "leaf 1-1-1",
                  value: "1-1-1"
                },
                {
                  title: "leaf 1-1-2",
                  value: "1-1-2"
                }
              ]
            },
            {
              title: "parent 1-2",
              value: "1-2",
              expand: true,
              children: [
                {
                  title: "leaf 1-2-1",
                  value: "1-2-1"
                },
                {
                  title: "leaf 1-2-1",
                  value: "1-2-2"
                }
              ]
            }
          ]
        }
      ]
    };
  },
  methods: {}
};
</script>

<style scoped lang="less">
.wrapper {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
