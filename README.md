# vue3-code-input
vue3 验证码输入框

![image](https://github.com/CryUshio/vue3-code-input/assets/30655354/6439caae-a5e8-4c0a-a0ea-ecc66544c874)

## 使用
```vue
<script setup>
import { VerificationInput } from 'vue3-code-input';
</script>
<template>
  <VerificationInput></VerificationInput>
</template>
```
样式可以自由覆盖。

## 参数
props|desc|type|default|required
--|--|--|--|--|
step      | 验证码个数 | `number`   | `6`      | -
autofocus | 原生属性   | `boolean`  | `false`  | -
disabled  | 是否禁用   | `boolean`  | `false`  | -
inputFormatter | 格式化输入值 | `(t: string) => string` | 仅允许数字和字母 | -

## 事件
events|desc|type
--|--|--|
change | 输入内容改变 | `(value: string) => void`
