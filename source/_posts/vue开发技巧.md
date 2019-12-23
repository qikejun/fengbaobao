---
title: vue开发技巧
date: 2019-11-14 11:30:19
categories:
  - Vue
  - 技巧
tags: Vue
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer8.jpg'
---

# Vue 开发技巧

## 1.require.context()

1. 使用场景：页面 需要导入多个组件

   一一导入：

   ```js
   import titleCom from '@/components/common/Tabs'
   import bannerCom from '@/components/common/Tips'
   import cellCom from '@/components/common/Header'
   //注册组件
   components: {Tabs, Tips, Header}
   ```

2. 使用 require.context

   ```js
   const path = require('path')
   const components = require.context('@/components/hemo', false, '/\.vue$/')
   const modules = {}
   components.keys().forEach(key => {
       const name = paht.basename(key, '.vue')
       modules[name]=components(key).default || components(key)
   })
   //注册组件
   components: modules
   ```

3. API

   > require.context(directory, useSubdirectories, regExp)
   >
   > 接收三个参数:  
   >
   > directory ：说明需要检索的目录 
   >
   > useSubdirectories ：是否检索子目录 
   >
   > regExp : 匹配文件的正则表达式,一般是文件名