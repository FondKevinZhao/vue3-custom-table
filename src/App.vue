<template>
  <div>
    <h1>App 根组件</h1>
    <hr />
    <MyTable :data="goodslist">
      <template v-slot:header>
        <th>#</th>
        <th>商品名称</th>
        <th>商品价格</th>
        <th>标签</th>
        <th>操作</th>
      </template>

      <template v-slot:body="{ row, index }">
        <td>{{ index + 1 }}</td>
        <td>{{ row.goods_name }}</td>
        <td>{{ row.goods_price }}</td>
        <!-- 标签信息普通写法：<td>{{ row.tags }}</td> -->
        <!-- 循环渲染标签信息   row.tags 是一个数组，所以要循环 -->
        <td>
          <!-- row里面有一个字段叫作：inputVal 一开始是空的-->
          <input
            type="text"
            class="form-control form-control-sm form-ipt"
            v-if="row.inputVisible"
            v-focus
            v-model.trim="row.inputVal"
            @blur="onInputConfirm(row)"
            @keyup.enter="onInputConfirm(row)"
            @keyup.esc="row.inputVal = ''"
          />
          <button
            v-else
            type="button"
            class="btn btn-primary btn-sm"
            @click="row.inputVisible = true"
          >
            +Tag
          </button>
          <span
            class="badge badge-warning ml-2"
            v-for="item in row.tags"
            :key="item"
            >{{ item }}</span
          >
        </td>
        <td>
          <button
            type="button"
            class="btn btn-danger btn-sm"
            @click="onRemove(row.id)"
          >
            删除
          </button>
        </td>
      </template>
    </MyTable>
  </div>
</template>

<script>
/* 
  表格案例：
    axios + table + 作用域插槽(获取表格数据) + 自定义指令(使input自动获取焦点) + v-model + @blur(input失去焦点，input消失，显示+Tag标签，并把值转成标签显示到页面上) + @keyup.enter="onInputConfirm(row)"(按下enter同样添加标签)
*/
import MyTable from "./components/my-table/MyTable.vue";
export default {
  name: "MyApp",
  components: { MyTable },
  data() {
    return {
      // 商品列表的数据
      goodslist: [],
    };
  },
  created() {
    // 发起请求
    this.getGoodsList();
  },
  methods: {
    async getGoodsList() {
      const { data: res } = await this.$http.get("/api/goods");
      // console.log(res);
      if (res.status !== 0) return console.log("获取商品列表失败！");
      this.goodslist = res.data;
    },
    // 根据id删除商品
    onRemove(id) {
      // console.log(id);
      var res = confirm("你确定删除吗？");
      if (res) {
        this.goodslist = this.goodslist.filter((x) => x.id != id);
      }
    },
    onInputConfirm(row) {
      // 把row.inputVal的值转存到val身上
      const val = row.inputVal;
      // console.log(val); // 打印input身上的值
      // 清空row.inputVal身上的值
      row.inputVal = "";
      // 把row身上的inputVisible重置为false，让这个input框消失
      row.inputVisible = false;

      // 判断刚才的val值是否为空，或者在row.tags数组里面已经存在了
      if (row.tags.indexOf(val) != -1) {
        return alert("输入的值已存在，请重新输入");
      }
      // 把刚才存到val身上的input的值，转存到row.tags数组身上
      row.tags.push(val);
    },
  },
  // 自定义指令
  directives: {
    focus(el) {
      el.focus();
    },
  },
};
</script>

<style lang="less" scoped>
.form-ipt {
  width: 80px;
  display: inline;
}
</style>
