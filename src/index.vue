<template>
  <div class="tt-wrapper">
    <div class="tt-list tt-list-origin">
      <div class="tt-list-header">
        <span class="tt-list-header-title">{{titles[0]}}</span>
        <span class="tt-list-header-count">{{originNodeCount}}</span>
      </div>
      <div class="tt-list-content">
        <Tree
          :data="origin"
          show-checkbox
          check-directly
          check-strictly
          :children-key="childrenKey"
          @on-check-change="arr => { checkingNodes = arr }"
        ></Tree>
      </div>
    </div>
    <div class="tt-operation">
      <Button
        class="tt-operation-btn"
        type="primary"
        icon="ios-arrow-back"
        :disabled="backDisabled"
        @click="handleBack"
      ></Button>
      <Button
        class="tt-operation-btn"
        type="primary"
        icon="ios-arrow-forward"
        :disabled="forwordDisabled"
        @click="handleForward"
      ></Button>
    </div>
    <div class="tt-list tt-list-target">
      <div class="tt-list-header">
        <span class="tt-list-header-title">{{titles[1]}}</span>
        <span class="tt-list-header-count">{{checkedNodes.length}}</span>
      </div>
      <div class="tt-list-content">
        <CheckboxGroup v-model="targetGroup">
          <ul class="tt-list-group">
            <li
              v-for="item in checkedNodes"
              :key="item[valueKey]"
              class="tt-list-group-item"
            >
              <Checkbox :label="item[valueKey]">{{item.title}}</Checkbox>
            </li>
          </ul>
        </CheckboxGroup>
      </div>
    </div>
  </div>
</template>

<script>
import { Tree, Button, Icon, CheckboxGroup, Checkbox } from "view-design";
import "view-design/dist/styles/iview.css";
import cloneDeep from "lodash.clonedeep";

export default {
  name: "index",
  components: {
    Tree,
    Button,
    Icon,
    CheckboxGroup,
    Checkbox
  },
  props: {
    // 标题
    titles: {
      type: Array,
      default: () => ["源列表", "目的列表"]
    },
    // 数据源
    dataSource: {
      type: Array,
      default: () => []
    },
    // 每一条数据唯一值的key
    valueKey: {
      type: String,
      default: "value"
    },
    // 子节点 key
    childrenKey: {
      type: String,
      default: "children"
    }
  },
  data() {
    return {
      origin: [], // 数据源（fork）
      originNodeCount: 0, // 数据源节点数（不含禁用的节点）
      checkingNodes: [], // 左侧列表选中的节点
      checkedNodes: [], // 点击按钮后传递到右侧的节点
      targetGroup: [] // 右侧列表选中的节点
    };
  },
  computed: {
    forwordDisabled() {
      return !this.checkingNodes.filter(item => !item.disabled).length;
    },
    backDisabled() {
      return !this.targetGroup.length;
    }
  },
  watch: {
    dataSource: {
      handler(val) {
        this.origin = cloneDeep(this.dataSource);
        this.traverseNodes(this.origin, node => {
          if (!node.disabled) this.originNodeCount += 1;
          if (!("checked" in node)) this.$set(node, "checked", false);
          if (!("disabled" in node)) this.$set(node, "disabled", false);
        });
      },
      immediate: true,
      deep: true
    }
  },
  methods: {
    handleForward() {
      this.checkedNodes = this.checkingNodes;
      // .filter(
      //   node => !node[this.childrenKey] || !node[this.childrenKey].length
      // );
      const checkedValues = this.checkedNodes.map(item => item[this.valueKey]);
      this.traverseNodes(this.origin, node => {
        if (checkedValues.includes(node[this.valueKey])) {
          this.$set(node, "checked", true);
          this.$set(node, "disabled", true);
        }
      });
    },
    handleBack() {
      this.traverseNodes(this.origin, node => {
        if (this.targetGroup.includes(node[this.valueKey])) {
          this.$set(node, "checked", false);
          this.$set(node, "disabled", false);
        }
      });
      this.checkedNodes = this.checkedNodes.filter(
        item => !this.targetGroup.includes(item[this.valueKey])
      );
      this.checkingNodes = cloneDeep(this.checkedNodes);
      this.targetGroup = [];
    },
    traverseNodes(data, cb) {
      data.forEach(node => {
        cb && cb(node);
        if (node[this.childrenKey] && node[this.childrenKey].length) {
          this.traverseNodes(node[this.childrenKey], cb);
        }
      });
    }
  }
};
</script>

<style scoped lang="less">
.tt-wrapper {
  display: flex;
}

.tt-list {
  width: 300px;
  height: 400px;
  border: 1px solid #dcdee2;
  border-radius: 6px;

  &-header {
    display: flex;
    justify-content: space-between;
    padding: 8px 16px;
    background: #f9fafc;
    color: #515a6e;
    border-bottom: 1px solid #e8eaec;
    border-radius: 6px 6px 0 0;
    overflow: hidden;
  }

  &-content {
    padding: 8px 0;
    overflow: auto;
  }

  &-group {
    &-item {
      padding: 8px 16px;

      &:hover {
        background-color: #f3f3f3;
      }
    }

    /deep/.ivu-checkbox-group-item {
      display: block;
    }
  }
}

.tt-operation {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 8px;

  &-btn + &-btn {
    margin-top: 8px;
  }
}
</style>
