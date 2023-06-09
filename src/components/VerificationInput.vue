<!-- eslint-disable func-call-spacing -->
<script lang="ts" setup>
import { reactive, ref } from "vue";

const props = withDefaults(
  defineProps<{
    step?: number;
    autofocus?: boolean;
    disabled?: boolean;
    inputFormatter?: (t: string) => string;
  }>(),
  {
    step: 6,
    inputFormatter: (t: string) => {
      return t.replace(/[^a-z0-9A-Z]/g, "").toUpperCase();
    },
  }
);

const emit = defineEmits<{
  (e: "change", value: string): void;
}>();

const inputValue = reactive(new Array(props.step).fill(""));

const inputRefs = ref<Record<number, HTMLInputElement>>({});

function handleChange(value: string, index: number) {
  const formattedValue = props.inputFormatter(value);

  if (formattedValue === "") {
    inputValue[index] = formattedValue;
    const inputRef = inputRefs.value[index];
    if (inputRef) {
      inputRef.value = inputValue[index];
    }
    emit("change", inputValue.join(""));

    return;
  }

  /** 输入框长度必须设置为大于 1，方便输入替换原来的值 */
  inputValue[index] = formattedValue.length === 1 ? formattedValue : formattedValue.replace(inputValue[index], "")[0];

  const inputRef = inputRefs.value[index];
  if (inputRef) {
    inputRef.value = inputValue[index];
  }

  inputRefs.value[Math.min(index + 1, props.step - 1)]?.focus();

  emit("change", inputValue.join(""));
}

/** 处理粘贴，必须 preventDefault，否则会粘贴到输入框，两边一起处理会混乱 */
function handlePaste(value: string, index: number) {
  const valueArr = props.inputFormatter(value).split("");

  if (valueArr.length === 0) {
    return;
  }

  for (let i = 0; i < valueArr.length; i++) {
    if (inputValue[index + i] === undefined) {
      break;
    }

    inputValue[index + i] = valueArr[i];
  }

  inputRefs.value[Math.min(index + value.length, props.step - 1)]?.focus();

  emit("change", inputValue.join(""));
}

/** 处理输入框本身为空的情况 */
function handleBackspace(index: number) {
  if (inputValue[index] !== "") {
    inputValue[index] = "";
    emit("change", inputValue.join(""));
    return;
  }

  if (index === 0) {
    return;
  }

  inputRefs.value[index - 1]?.focus();
}

function handleArrayLeft(index: number) {
  if (index === 0) {
    return;
  }

  inputRefs.value[index - 1]?.focus();
}

function handleArrayRight(index: number) {
  if (index === props.step - 1) {
    return;
  }

  inputRefs.value[index + 1]?.focus();
}
</script>
<template>
  <div class="verification-input">
    <input
      class="verification-input__inner"
      v-for="i in step"
      ref="inputRefs"
      type="text"
      :key="i"
      :autofocus="autofocus && i === 1"
      :value="inputValue[i - 1]"
      :readonly="disabled"
      :disabled="disabled"
      :maxlength="2"
      @paste.prevent="handlePaste($event.clipboardData?.getData('Text') || '', i - 1)"
      @keydown.left="handleArrayLeft(i - 1)"
      @keydown.right="handleArrayRight(i - 1)"
      @keydown.backspace="handleBackspace(i - 1)"
      @input.prevent.stop="(e) => handleChange((e.target as HTMLInputElement).value, i - 1)"
    />
  </div>
</template>

<style lang="less" scoped>
.verification-input {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;

  .verification-input__inner {
    border: none;
    outline: none;
    box-shadow: 0 -2px 0 -1px #ccc inset;
    margin-right: 8px;
    height: 48px;
    width: 48px;
    text-align: center;
    font-size: 20px;
    transition: all 0.2s;
    ime-mode: disabled;

    &:hover,
    &:focus {
      box-shadow: 0 -2px 0 -1px #409eff inset;
    }

    &[disabled] {
      box-shadow: 0 -2px 0 -1px #d2d2d2 inset;
    }

    &:last-child {
      margin-right: 0;
    }
  }
}
</style>
