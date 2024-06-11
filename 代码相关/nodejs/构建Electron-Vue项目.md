编写日期：2024-04-17 
教程参考：[Electron + Vue3 开发跨平台桌面应用【从项目搭建到打包完整过程】 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/483470991)
安装node版本管理器NVM,作用是可以很方便安装和切换各个nodejs版本

下载地址：[Releases · coreybutler/nvm-windows (github.com)](https://github.com/coreybutler/nvm-windows/releases)

下载`[nvm-setup.exe]`即可

在cmd中输入命令 `nvm list`查看是否安装成功

本教程使用vscode作为开发工具

下载地址：[Visual Studio Code - Code Editing. Redefined](https://code.visualstudio.com/)

下面的所有命令行都可以在vscode的终端中执行

可以通过vscode上方的菜单栏打开终端

# 1. 检查node版本
本教程将使用 v16.16.0，一定不要更改版本，不然会导致各种因为版本不兼容导致的报错
```
--查看版本
nvm list
--下载
nvm install 16.16.0
--切换
nvm use 16.16.0
```

# 2. 安装必要的模块
```
--切换npm源
npm config set registry http://registry.npm.taobao.org/
--npm不做严格ssl校验
npm config set strict-ssl false
--安装pnpm(用这个代替npm)
npm install -g pnpm@8
--pnpm换源
pnpm config set registry https://registry.npmmirror.com
--安装yarn
pnpm install -g yarn
--切换yarn源
yarn config set registry https://r.cnpmjs.org
--yarn不做严格ssl校验
yarn config set strict-ssl false
--安装vue/cli
pnpm install @vue/cli -g
yarn add @vue/cli -g
```

# 3. 创建项目

在你想要存放项目的文件夹下右键鼠标打开VSCODE，然后打开终端执行以下操作
```
vue create eletron_vue
```
执行后会让你选择创建项目的方案,里面预存了两种方案
第一次使用选择Manually select features（手动选择安装项）回车确认
![[Pasted image 20240425095552.png]]

接下来是让你选择需要用到的组件，我们这里选择TypeScript，Router和Vuex，这三个组件的作用我们之后会提到（空格选择，回车确定）


![[Pasted image 20240425095812.png]]

接下来是选择vue的版本，我们选择3.x

![[Pasted image 20240425100011.png]]

接下来是一大堆选项,输入选项后面的选择

- Use class-style component syntax? (y/N) 回答：n
- Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? (Y/n) 回答：n
- Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n) n
- Pick a linter / formatter config 回答：ESLint + Prettier | Lint on save
- Where do you prefer placing config for Babel, ESLint, etc.? 回答：In dedicated config files
- Save this as a preset for future projects? 回答：y，然后自己起个名，下次就可以直接用了

<details> 
<summary>解释</summary> 
<pre><blockcode> 
1. **Use class-style component syntax? (y/N)** 
   这个问题询问是否使用基于类的组件语法。基于类的组件是React中一种编写组件的方式，不过近年来，函数式组件配合Hooks已经成为更受欢迎的方式。回答 'n' 表示不使用基于类的组件语法，而是选择函数式组件或其他方式。
2. **Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? (Y/n)** 
   这个问题询问是否在TypeScript项目中使用Babel。Babel是一个JavaScript编译器，主要用于将ES6+代码转换为向后兼容的JavaScript版本，同时也可以处理JSX。尽管TypeScript自身可以转译JavaScript，但Babel提供了更多配置选项和插件，可以进行更细粒度的控制。回答 'n' 表示不使用Babel，可能是因为项目不需要复杂的转译需求，或者选择了其他工具。
3. Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n)
   在 Vue 3 的 `vue-router` 中，`history mode` 是一种路由模式，它允许你使用 `pathname` 作为 URL，而不是传统的 `hash` 模式。在 `history mode` 下，URL 看起来像这样：`https://example.com/user/profile`，而不是 `https://example.com/#/user/profile`。
   这里我们还是使用hash模式，因为electron不支持history模式。其他具体优劣可以看这里：[vue路由模式及 history 模式下服务端配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/557638376)
4. **Pick a linter / formatter config**  
   这个问题让用户选择代码检查和格式化的配置。ESLint和Prettier是非常流行的代码质量和格式化工具。选择“ESLint + Prettier | Lint on save”表示希望在保存文件时自动执行ESLint和Prettier，以保持代码的整洁和质量。
5. **Where do you prefer placing config for Babel, ESLint, etc.?**  
   这个问题询问用户希望将Babel、ESLint等工具的配置放在哪里。选择“In dedicated config files”表示配置将会放在独立的配置文件中，例如`.babelrc`、`.eslintrc`等，而不是放在`package.json`中。
6. **Save this as a preset for future projects?**  
   这个问题询问是否保存当前的配置作为一个预设，以便在未来的项目中直接使用。回答 'y' 并起一个名字，可以将当前的配置保存下来，下次创建项目时就可以直接选择这个预设，省去了重复配置的步骤。
</blockcode></pre>
</details>

安装完成后测试是否成功

```
cd eletron_vue
yarn serve
```

浏览器打开http://localhost:8080/
出现正常网页则成功
Ctrl + C 退出

# 4. 安装插件
```
vue add electron-builder 
--会让你选择Electron的版本，选13.0.0
--如果出现这个错误：Error: command failed: yarn 
--直接执行以下命令
yarn
```

接下来我们需要删除vue-devtools的安装相关的代码，因为网络原因我们一般无法安装这个工具，但是每次启动它都会尝试安装，导致启动变慢

需要修改的文件在src/background.ts

删除引用

```
import installExtension, { VUEJS3_DEVTOOLS } from "electron-devtools-installer";
```

搜索`app.on("ready", async ()`

然后删除以下代码

```typescript
if (isDevelopment && !process.env.IS_TEST) {

    // Install Vue Devtools

    try {

      await installExtension(VUEJS3_DEVTOOLS);

    } catch (e) {

      console.error("Vue Devtools failed to install:", e.toString());

    }

  }
```

如果想要使用vue/devtools，参考以下步骤

```
--安装 vue/devtools
npm install -g @vue/devtools
--打开 vue/devtools
./node_modules/.bin/vue-devtools
--打开后将对应的script地址添加到html文件的head里面，然后再打开对应网页
```

安装ts-loader

```
yarn remove ts-loader
yarn add ts-loader@~8.2.0 -D
```

测试是否安装electron成功

```
yarn electron:serve
```

出现一个应用窗口则成功

文件夹结构如下：
![[v2-e7b0e1e0cdb7f9e176631376ccc6f56e_1440w.webp]]

# 5.引入UI框架

本教程将使用element plus作为UI框架

官网：[一个 Vue 3 UI 框架 | Element Plus (element-plus.org)](https://element-plus.org/zh-CN/)

安装

```
yarn add element-plus
```

在程序中引入
因为本教程面对的是新手，所以采用了最简单的全局引入，相对于按需引入来说使用更加方便但是性能更差

```
// 修改src/main.ts
import { createApp } from "vue";

import router from "./router";

import store from "./store";

import ElementPlus from "element-plus";

import "element-plus/dist/index.css";

import App from "./App.vue";

  

const app = createApp(App);

  

app.use(store);

app.use(router);

app.use(ElementPlus);

  

app.mount("#app");
```

新建文件src/components/ButtonsExample.vue

```
<template>

  <div class="mb-4">

    <el-button>Default</el-button>

    <el-button type="primary">Primary</el-button>

    <el-button type="success">Success</el-button>

    <el-button type="info">Info</el-button>

    <el-button type="warning">Warning</el-button>

    <el-button type="danger">Danger</el-button>

  </div>

  <div class="mb-4">

    <el-button plain>Plain</el-button>

    <el-button type="primary" plain>Primary</el-button>

    <el-button type="success" plain>Success</el-button>

    <el-button type="info" plain>Info</el-button>

    <el-button type="warning" plain>Warning</el-button>

    <el-button type="danger" plain>Danger</el-button>

  </div>

  

  <div class="mb-4">

    <el-button round>Round</el-button>

    <el-button type="primary" round>Primary</el-button>

    <el-button type="success" round>Success</el-button>

    <el-button type="info" round>Info</el-button>

    <el-button type="warning" round>Warning</el-button>

    <el-button type="danger" round>Danger</el-button>

  </div>

  

  <div>

    <el-button :icon="Search" circle />

    <el-button type="primary" :icon="Edit" circle />

    <el-button type="success" :icon="Check" circle />

    <el-button type="info" :icon="Message" circle />

    <el-button type="warning" :icon="Star" circle />

    <el-button type="danger" :icon="Delete" circle />

  </div>

</template>

  

<script lang="ts" setup>

import {

  Check,

  Delete,

  Edit,

  Message,

  Search,

  Star,

} from "@element-plus/icons-vue";

</script>
```

然后修改app.vue

```
<template>

  <nav>

    <router-link to="/">Home</router-link> |

    <router-link to="/about">About</router-link>

  </nav>

  <router-view />

  <ButtonsExample />

</template>

  

<script setup>

import ButtonsExample from "./components/ButtonsExample.vue";

</script>

  

<style>

#app {

  font-family: Avenir, Helvetica, Arial, sans-serif;

  -webkit-font-smoothing: antialiased;

  -moz-osx-font-smoothing: grayscale;

  text-align: center;

  color: #2c3e50;

}

  

nav {

  padding: 30px;

}

  

nav a {

  font-weight: bold;

  color: #2c3e50;

}

  

nav a.router-link-exact-active {

  color: #42b983;

}

</style>
```

测试

```
yarn electron:serve
```

出现以下画面则成功

多出来的按钮就是源自element plus 的

![[Pasted image 20240425113541.png]]