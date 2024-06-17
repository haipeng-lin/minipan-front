<template>
  <!-- v-html指令将excelContent的内容渲染为HTML -->
  <div v-html="excelContent" class="talbe-info"></div>
</template>

<script setup>
// 使用XLSX库来处理Excel文件
import * as XLSX from "xlsx";
import { ref, reactive, getCurrentInstance, onMounted } from "vue";

// 通过getCurrentInstance()获取当前组件实例
const { proxy } = getCurrentInstance();

// 通过getCurrentInstance()获取当前组件实例。
const props = defineProps({
  url: {
    type: String,
  },
});

// 使用ref创建一个响应式变量excelContent，用于存储解析后的Excel表格HTML内容
const excelContent = ref();

// 初始化Excel函数
const initExcel = async () => {
  let result = await proxy.Request({
    url: props.url,
    responseType: "arraybuffer",
  });
  if (!result) {
    return;
  }
  // 解析数据
  let workbook = XLSX.read(new Uint8Array(result), { type: "array" });
  // workbook.SheetNames 下存的是该文件每个工作表名字,这里取出第一个工作表
  var worksheet = workbook.Sheets[workbook.SheetNames[0]]; 
  excelContent.value = XLSX.utils.sheet_to_html(worksheet);
};

// 钩子函数
onMounted(() => {
  initExcel();
});
</script>

<style lang="scss" scoped>
.talbe-info {
  width: 100%;
  padding: 10px;
  :deep table {
    width: 100%;
    border-collapse: collapse;
    td {
      border: 1px solid #ddd;
      border-collapse: collapse;
      padding: 5px;
      height: 30px;
      min-width: 50px;
    }
  }
}
</style>
