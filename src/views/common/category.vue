<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    node-key="catId"
    ref="menuTree"
    @node-click="nodeclick"
  >
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      expandedkey: [],
      defaultProps: {
        //返回数据的子节点
        children: "children",
        //需要显示的字段
        label: "name",
      },
    };
  },
  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        methods: "get",
      }).then(({ data }) => {
        this.menus = data.data;
      });
    },
    nodeclick(data, node, component) {
      this.$emit("tree-node-click",data, node, component);
    },
  },
  created() {
    this.getMenus();
  },
};
</script>