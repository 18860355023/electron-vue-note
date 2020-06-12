### electron-vue开发踩坑之旅

1.  element-UI渲染表格失败

问题描述: 在使用github上集成的electron-vue框架开发项目时，引入了element-ui第三方模块。
然而在使用表格组件时，页面始终一片空白。用F12审查元素，发现表格的高度始终为0。
排除各种参数的影响之后，估计是渲染失败。

解决方案: 
    打开`.electron-vue`目录下的`webpack.renderer.config.js`文件
    搜索`whiteListedModules`
    将这一行修改为   

```JavaScript
let whiteListedModules = ['vue', 'element-ui']
```

2. electron-vue 与node.js版本兼容性问题

   问题描述: 使用node.js-10.16版本以上及以下则会出现自动打开文件资源管理器的问题

   解决方案:

   ​	降低node.js版本到10.16

   ​	使用yarn命令安装依赖

3. electron-vue自定义标题栏

   1. mainWindow中添加 frame：false

   ```js
   mainWindow = new BrowserWindow({    
       height: 563,
       useContentSize: true,
       width: 1000,
       frame: false,//是否显示窗口边框
   });
   ```

   2. 手动创建一个标题栏，使用 `-webkit-app-region: drag;`可以让标题栏可拖动，使用`-webkit-user-select: none;`可以让目标区域不可选中，使用`-webkit-app-region: no-drag;`可以让目标区域不可拖动

   ```html
   <el-row style="-webkit-app-region: drag;-webkit-user-select: none;">    	<el-col :span="3" style="-webkit-app-region: no-drag;">        
       <el-col :span="6">
           <img id="logo" src="~@/assets/control.png" alt="electron-vue">		</el-col>        
       <el-col :span="18">
           <span class="t-name">自动化测试</span></el-col>
       </el-col>    
       <el-col :span="17">
           <span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
       </el-col>
       <el-col :span="4" style="-webkit-app-region: no-drag;" class="btn-			option">
           <i title="最小化" class="mini noDrag el-icon-minus bq" 					@click="setWin('min')"></i>
           <i title="最大化" class="max noDrag el-icon-news bq" 						@click="setWin('max')"></i>
           <i title="关闭" class="close noDrag el-icon-close bq" 					@click="setWin('close')"></i>
       </el-col>
   </el-row>
   ```

   3. 向electron中ipcRenderer组件中发送指令

      ```javascript
      const {ipcRenderer} = require("electron");
      methods: {
          setWin(type) {
              ipcRenderer.send(type);
          },
      },
      ```

   4. 接收指令并实现对应功能

      ```js
      // 自定义顶部菜单栏,实现放大缩小关闭功能
      import {ipcMain} from 'electron'
      ipcMain.on('min', () => mainWindow.minimize());
      ipcMain.on('max', () => {    
          if (mainWindow.isMaximized()) {        
              mainWindow.restore();    
          } else {        
                  mainWindow.maximize()    
              }
      });
      ipcMain.on('close', () => mainWindow.close());
      ```

4. 隐藏顶部菜单（需先引入Meun），不开启调试控制台

   ```js
   import {Menu} from 'electron'
   // 隐藏顶部菜单(需引入Menu)
   Menu.setApplicationMenu(null);
   mainWindow.loadURL(winURL);
   // 默认不开启调试控制台
   mainWindow.webContents.closeDevTools();
   ```
   
5. electron-vue固定导航栏到顶部(类似吸顶样式)

   ```css
   position: sticky;
   position: -webkit-sticky; /*兼容 -webkit 内核的浏览器 */
   top: 0; /*必须设一个值，否则不生效*/
   z-index: 999;  /*设置优先层级,优先级低,滚动内容会覆盖顶部导航栏*/
   ```

6. 隐藏滚动条

   ```css
   ::-webkit-scrollbar {    
       width: 0 !important;
   }
   ::-webkit-scrollbar {    
       width: 0 !important;
       height: 0;
   }
   ```

7. electron-vue 调用后端接口存在的跨域问题

   解决方案: 一开始查找了vue使用axios跨域的问题如何解决，但是electron-vue中没有config文件夹，后来查找了electron-vue的axios跨域问题

   在.electron-vue文件夹下dev-runner.js中修改如下代码:

   ```javascript
   const server = new WebpackDevServer(
         compiler,
         {
           contentBase: path.join(__dirname, '../'),
           quiet: true,
           proxy: {
             '/httpUrl': {
               // 请求的目标服务器地址
               target: '指向你的服务器地址',
               // 设置允许跨域
               changeOrigin: true,
               // 重写路径
               pathRewrite: {
                 '^/httpUrl': ''
               },
               headers: {
                 referer: ''
               }
             }
           },
           before (app, ctx) {
             app.use(hotMiddleware)
             ctx.middleware.waitUntilValid(() => {
               resolve()
            })
           }
         }
       )
   ```
   
8. vue websocket连接测试

   http://www.websocket-test.com/

9. 创建子窗口禁用父窗口

   ![1591689942077](C:\Users\Whu\AppData\Roaming\Typora\typora-user-images\1591689942077.png)

10. vue 模拟点击事件(点击一个按钮,执行另一个按钮的事件)

    · 创建点击函数,指定参数,区分点击的是哪个按钮,给要执行的按钮添加ref事件

    · 使用this.$refs.ctl.$el.click()调用

    ![1591771770690](C:\Users\Whu\AppData\Roaming\Typora\typora-user-images\1591771770690.png)

    ![1591771866537](C:\Users\Whu\AppData\Roaming\Typora\typora-user-images\1591771866537.png)

11. 
