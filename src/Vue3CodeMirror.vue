<script lang="ts">
//@ts-nocheck
import _CodeMirror from "codemirror/lib/codemirror";
import "codemirror/theme/darcula.css";
import "codemirror/addon/hint/show-hint.css";
import "codemirror/mode/clike/clike.js";
import "codemirror/mode/r/r.js";
const CodeMirror = window.CodeMirror ?? _CodeMirror;
let editor = null;
// const themeList = [];
// const themeModules = require.context("codemirror/theme/", false, /\.css$/);
// themeModules.keys().forEach((value) => {
//   const newValue = value.replace(/^\.\//, "").replace(/\.css$/, "");
//   themeList.push(newValue);
// });
</script>

<script setup lang="ts">
import {
  defineProps,
  reactive,
  ref,
  onMounted,
  onBeforeMount,
  getCurrentInstance
} from "@vue/runtime-core";
  console.log('setup...')
const props = defineProps({
  height: {
    type: Number,
    default: 500,
  },
  modelValue: {
    type: String,
    default: "",
  },
  scene: {
    type: String,
    value: "edit", // add look edit
  },
  emitEvent: {
    type: String,
    default: "blur",
  },
  theme: {
    type: String,
    default: "darcula",
  },
});
const textareaRef = ref(null)
const state = reactive({
  code: props.modelValue,
  options: {
    mode: "text/x-java",
    tabSize: 2,
    theme: props.theme,
    lineNumber: true,
    readOnly:
      props.scene === "add" || props.scene === "edit" ? false : "nocursor",
  },
});
const importThemes = () => {
  return new Promise((resolve) => {
    themeList.forEach((value) => {
      if (props.theme === value) {
        import(`codemirror/theme/${props.theme}.css`);
        resolve();
      }
    });
  });
};
const init = () => {
  console.log(textareaRef.value)
  editor = CodeMirror.fromTextArea(textareaRef.value, state.options);
  const height = props.heightSize + "px";
  editor.setSize("auto", height);
  editor.on(props.emitEvent, (editor) => {
    emit("update:modelValue", editor.getValue());
  });
};

onMounted(() => {
  console.log('init...')
  // importThemes().then(() => {
    init();
  // });
});
onBeforeMount(() => {
  console.log('onBeforeMount...')
  editor?.off(props.emitEvent);
});
</script>

<template>
    <textarea
      style="height: 100% !important"
      ref="textareaRef"
      v-model="state.code"
    ></textarea>
</template>
