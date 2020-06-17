##### electron-vue  开发

1. 安装node(10.16.0版本最好用)

2. 安装vue-cli

   ```js
   npm install vue-cli
   ```

3. 初始化electron-vue项目

   ```js
   vue init simulatedgreg/electron-vue  myproject  //使用simulatedgreg/electron-vue创建项目
   
   cd myproject  // 进入项目的文件夹内
   
   yarn & npm install   // 安装依赖
   
   yarn run dev & npm run dev  //进入开发模式(启动项目)
   ```

4. 安装和使用elemen-ui  UI库

   ```js
   npm install element-ui -S   // 安装element-ui
   ```

   ```javascript
   // 使用element-UI
   import Vue from 'vue';
   import ElementUI from 'element-ui';
   import 'element-ui/lib/theme-chalk/index.css';
   import App from './App.vue';
   
   Vue.use(ElementUI);
   
   new Vue({
     el: '#app',
     render: h => h(App)
   });
   ```

5. 打包electron-vue

   