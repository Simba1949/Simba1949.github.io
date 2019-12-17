# vue-cli 创建项目

## 前言

博客书

版本说明

```properties
vue=2.x
vue-cli=3.11.0
```

相关链接

* Vue 官网：https://cn.vuejs.org/
* vue-cli 官网：https://cli.vuejs.org/zh/guide/
* bable 官网：https://www.babeljs.cn/
* TypeScript 官网：https://www.tslang.cn/
* Vuex 官网：https://vuex.vuejs.org/
* Sass 官网：https://www.sass.hk/
* vue-router 官网：https://router.vuejs.org/zh/installation.html
* vue-axios Github 地址：https://github.com/axios/axios
* Element-UI 官网：https://element.eleme.cn/#/zh-CN
* IView-UI 官网：https://www.iviewui.com/

## 创建

### 创建

执行 `vue create hello-world` 进行项目创建

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: (Use arrow keys)
> default (babel, eslint)
  Manually select features
```

* default (babel, eslint) ：使用默认配置，包含babel, eslint两项
* Manually select features ：手动配置（推荐）

### 选定需要配置项

选择 `Manually select features` 手动配置项

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection)
>(*) Babel
 ( ) TypeScript
 (*) Progressive Web App (PWA) Support
 (*) Router
 (*) Vuex
 (*) CSS Pre-processors
 (*) Linter / Formatter
 ( ) Unit Testing
 ( ) E2E Testing  
```

* Babel：是一个 JavaScript 编译器；（必选）
* TypeScript：TypeScript是JavaScript类型的超集，它可以编译成纯JavaScript；
* Progressive Web App (PWA) Support：渐进式Web应用（PWA）支持；（推荐）
* Router：路由管理；（推荐）
* Vuex：是一个专为 Vue.js 应用程序开发的状态管理模式；（推荐）
* CSS Pre-processors：选择CSS 预处理类型；（推荐）
* Linter / Formatter：代码语法校验和格式化；（推荐）
* Unit Testing：单元测试；
* E2E Testing ：端到端测试；

### 具体配置项

#### 路由模式

选择`Babel、Progressive Web App (PWA) Support、Router、Vuex、CSS Pre-processors、Linter / Formatter` ，也自行选择

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n) y
```

* 是否使用 history 路由模式，默认为 hash 模式，hash 模式访问路径带有 `#` ，推荐 `history`

#### CSS 样式处理类型

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): (Use arrow keys)
> Sass/SCSS (with dart-sass)
  Sass/SCSS (with node-sass)
  Less
  Stylus
```

* Sass/SCSS (with dart-sass) ：dart-sass需要保存后才会生效；（推荐）
* Sass/SCSS (with node-sass)：node-sass是自动编译实时的；
* Less：
* Stylus：

#### 语法校验

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass)
? Pick a linter / formatter config: (Use arrow keys)
> ESLint with error prevention only
  ESLint + Airbnb config
  ESLint + Standard config
  ESLint + Prettier  
```

* ESLint with error prevention only ：仅错误预防
* ESLint + Airbnb config ：Airbnb配置
* ESLint + Standard config ：标准配置
* ESLint + Prettier ：Prettier 统一代码风格；（推荐）

#### 校验规则

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass)
? Pick a linter / formatter config: Prettier
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)
>(*) Lint on save
 ( ) Lint and fix on commit   
```

* Lint on save ：保存时；（推荐）
* Lint and fix on commit ：提交时；

#### 配置文件存储

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass)
? Pick a linter / formatter config: Prettier
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? (Use arrow keys)
> In dedicated config files
  In package.json     
```

* In dedicated config files ：在独立文件；（推荐）
* In package.json ：再  package.json 文件中；

#### 是否保存当前配置

```
Vue CLI v3.11.0
┌───────────────────────────┐
│  Update available: 4.1.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass)
? Pick a linter / formatter config: Prettier
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? (y/N)    
```

* Save this as a preset for future projects 是否为以后项目保存当前配置项；

### 推荐总结

```
(*) Babel
(*) Progressive Web App (PWA) Support
(*) Router ————> history
(*) Vuex
(*) CSS Pre-processors ————> Sass/SCSS (with dart-sass)
(*) Linter / Formatter ————> ESLint + Prettier
```

## 添加插件

### vue-router

```
# 安装
npm install vue-router
# 引入
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
```

### vue-axios

```
# 安装
npm install --save axios vue-axios
# 引入
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios, axios)
```

## 添加第三方UI

### Element-UI

```
# 安装
npm i element-ui -S
# 引入
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
Vue.use(ElementUI);
```

### IView-UI

```
# 安装
npm install view-design --save
# 引入
import ViewUI from 'view-design';
import 'view-design/dist/styles/iview.css';
Vue.use(ViewUI);
```

