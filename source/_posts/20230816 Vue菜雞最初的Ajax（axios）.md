---
title: 20230816 Vue菜雞最初的Ajax（axios）+Django記得加跨域標頭（未完）
date: 2023-08-16T21:49:55.971
tag:
  - Vue
  - Django
updated: 2023-08-16T21:49:55.971
categories:
  - [Vue]
  - [Django]
---
### 閱讀文章
- [在Vue.js中使用Axios取得資料](https://medium.com/web-design-zone/%E5%9C%A8vue-js%E4%B8%AD%E4%BD%BF%E7%94%A8axios%E5%8F%96%E5%BE%97%E8%B3%87%E6%96%99-8db6aec9157d)
- Replit的範本，不過似乎跟我想設計的情境不太一樣@@
App.vue
```js
//import HelloWorld
<template>
      <HelloWorld msg="You did it!" />
</template>
```
Hello World.vue
```js
<script setup>
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>
<template>
  {{msg}}
</template>
```
- [跨域問題](https://ithelp.ithome.com.tw/articles/10267360)
  - [Django跨域標頭](https://ithelp.ithome.com.tw/articles/10234669)
  - 看來在Django那邊標頭寫完整前還不能串，所以...
#### 先想練習讓Vue的模板讀得到script setup的變數吧
TotalAssets.vue
```js
<script setup>
const totalAssets = 'test'
</script>
<template>
{{ totalAssets }}
</template>
```
在其他地方插入這個TotalAssets.vue就會顯示test喔

[這麼簡短就是Vue3 script setup語法糖的威力](https://stackoverflow.com/questions/73134550/how-do-you-get-variables-from-the-setup-script-in-vue3)