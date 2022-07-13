<template>
  <table class="table table-bordered table-striped">
    <!-- 表格的标题区域 -->
    <thead>
      <tr>
        <!-- 标题不写死，用户通过插槽的方式自定义传过来 -->
        <!-- 命名插槽 -->
        <slot name="header"></slot>
      </tr>
    </thead>

    <!-- 表格的主题区域 -->
    <tbody>
      <!-- 使用 v-for 指令，循环渲染表格的数据行 -->
      <tr v-for="(item, index) in data" :key="item.id">
        <slot name="body" :row="item" :index="index"></slot>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  name: "MyTable",
  props: {
    data: {
      type: Array,
      required: true,
      default: [],
    },
  },
};
</script>

<style lang="less" scoped>
</style>