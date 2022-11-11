<template>
  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      :expand-on-click-node="false"
      show-checkbox
      node-key="catId"
      :default-expanded-keys="expandedKey"
    >
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level <= 2"
            type="text"
            size="mini"
            @click="() => append(data)"
          >
            添加
          </el-button>
          <el-button
            v-if="node.childNodes.length == 0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >
            删除
          </el-button>
        </span>
      </span>
    </el-tree>
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
      <el-form :model="category">
        <el-form-item label="分类名称">
          <el-input v-model="category.name" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCategory()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  components: {},
  props: {},
  data() {
    return {
      menus: [],
      category: { name: "",parentCid:0,catLevel:0,showStatus:1,sort:0 },
      dialogVisible: false,
      expandedKey: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  methods: {
    //拿到所有数据的方法
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        console.log("成功获取到数据", data.data);
        this.menus = data.data;
      });
    },
    //点击添加后，调用后端接口，弹出添加成功窗口，把输入框隐藏，刷新页面，
    //调用default-expanded-keys的属性,默认展开的节点的 key 的数组,让页面在指定的id的属性下打开
    
    addCategory() {
        console.log("category",this.category)
      this.$http({
        url: this.$http.adornUrl("/product/category/save"),
        method: "post",
        data: this.$http.adornData(this.category, false),
      }).then(({ data }) => {
        
        this.$message({
            type: "success",
            message: "菜单添加成功",
          });
          this.dialogVisible= false,
          this.getMenus();
          this.expandedKey = [this.category.parentCid];
      });
    },
    // 点击按钮，添加框弹出，数据初始化
    append(data) {
        console.log("update的data",data)
      this.dialogVisible = true;
      this.category.parentCid=data.catId;
      this.category.catLevel=data.catLevel*1+1;

    },
//移除数据的方法
    remove(node, data) {
      var ids = [data.catId];
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          }).then(({ data }) => {
            console.log("删除成功");
            this.getMenus();
            this.expandedKey = [node.parent.data.catId];
          });
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
  created() {
    this.getMenus();
  },
};
</script>

<style scoped>
</style>
