<template>
  <!-- 预览word文档，使用ref属性并在脚本中定义相应的引用，可以获取到特定的DOM元素 -->
  <div ref="docRef" class="doc-content"></div>
</template>

<script setup>
// 引入docx-preview：用于渲染Word文档内容到HTML
import * as docx from "docx-preview";
import { ref, reactive, getCurrentInstance, onMounted } from "vue";

// 获取当前实例
const { proxy } = getCurrentInstance();

// 使用defineProps定义组件的props，其中包括一个url属性，类型为String，用于传递Word文档的URL
const props = defineProps({
  url: {
    type: String,
  },
});

// 使用ref定义了一个响应式引用docRef，用于指向DOM元素
const docRef = ref();

// 初始化word文档
const initDoc = async () => {
  let result = await proxy.Request({
    url: props.url,
    responseType: "blob",
  });
  if (!result) {
    return;
  }
  // renderAsync方法：将文档渲染到docRef引用的DOM元素中
  docx.renderAsync(result, docRef.value);
};

// 钩子函数，调用初始化函数
onMounted(() => {
  initDoc();
});
</script>

<style lang="scss" scoped>
.doc-content {
  margin: 0px auto;
  :deep .docx-wrapper {
    background: #fff;
    padding: 10px 0px;
  }

  :deep .docx-wrapper > section.docx {
    margin-bottom: 0px;
  }
}
</style>
