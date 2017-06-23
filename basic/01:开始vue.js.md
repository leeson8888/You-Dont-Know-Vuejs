# 本节目录

* 安装vue.js
* 起步vue.js


# 安装vue.js

>Vue.js 不支持 IE8 及其以下版本，因为 Vue.js 使用了 IE8 不能模拟的 ECMAScript 5 特性。

查看vue.js 2.x源代码仓库，发现构建后的dist目录有8个文件：


| | UMD | CommonJS | ES Module |
| --- | --- | --- | --- |
| **Full**  | vue.js | vue.common.js | vue.esm.js |
| **Runtime-only** | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js |
| **Full (production)** | vue.min.js | | |
| **Runtime-only (production)** | vue.runtime.min.js | | |

注：因为vue.min.js，vue.runtime.min.js 属于压缩后的版本，因此**实际上只有6个版本**。

#### 独立构建-vs-运行时构建-vs-完整构建


**独立构建**包含模板编译器，职责是将模板字符串编译为纯 JavaScript 的渲染函数, 因此独立构建时可以在组件中使用 template 选项。

**独立构建**依赖于浏览器的接口的存在，所以不能使用它来为服务器端渲染。

**运行时构建**不包含模板编译器，因此不支持 template 选项，只能用 render 选项,要轻量30%。

vue.runtime.js，vue.runtime.common.js，vue.runtime.esm.js 属于运行时构建。

**完整构建**包含:独立构建和运行时构建

------
>由于 Vue.js 1.0 的编译过程需要依赖浏览器的 DOM，所以无法（或者说没有意义）将编译器和运行时分开。因此在 Vue.js 1.0 分发包中，编译器和运行时是打包在一起，都在浏览器端执行。

>然而到了 Vue.js 2.0，为了支持服务端渲染（server-side rendering），编译器不能依赖于 DOM，所以必须将编译器和运行时分开。这就形成了独立构建（编译器 + 运行时）和运行时构建（仅运行时）。显而易见，运行时构建要小于独立构建。

>在现代前端工程构建中，通常会使用 vue-loader 和 vueify 预编译模板。在这种情况下，只需要打包运行时，而不需要打包编译器，运行时构建即可满足所需。当然，如果你需要在前端使用 template 选项实时编译模板，那么还是需要使用独立构建将编译器发送到浏览器。

参考：https://jingsam.github.io/2016/10/23/standalone-vs-runtime-only-build-in-vuejs2.html?utm_source=tuicool&utm_medium=referral

------


vue.js, vue.common.js,vue.esm.js 都属于完整构建

#### UMD, CommonJS 和 ES Module规范

UMD：UMD规范，同时适用于浏览器与Commonjs环境

CommonJS：适用于Commonjs环境,如nodejs

ES Module：ES2015(es6)的模块（module）体系


#### vue.common.js

基于 CommonJS 的完整构建
可以用于 Webpack1 和 Browserify 之类打包工具。组件中可以使用template
```js
import Vue from 'vue'
new Vue({
  template: `
    <div id="app">
      <h1>Basic</h1>
    </div>
  `
}).$mount('#app')

//用 webpack-1 之类打包工具时, 使用该版本, 需要配置别名
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.common.js' // 'vue/dist/vue.esm.js' for webpack 2
    }
  }
}
```

#### vue.esm.js

属于: 基于 ES Module 的完整构建
可以用于 Webpack-2 和 rollup 之类打包工具
因为是完整构建, 所以可以使用template选项。

```js
import Vue from 'vue'
new Vue({
  template: `
    <div id="app">
      <h1>Basic</h1>
    </div>
  `
}).$mount('#app')

//webpack2 打包配置
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' 
    }
  }
}
//rollup打包配置
const alias = require('rollup-plugin-alias')

rollup({
  // ...
  plugins: [
    alias({
      'vue': 'vue/dist/vue.esm.js'
    })
  ]
})

```



#### vue.js

包含独立和运行时构建
```js
<script src="https://unkpg.com/vue/dist/vue.js"></script>

<script>
new Vue({
  template: `
    <div id="app">
      <h1>Hi Vue</h1>
    </div>
  `
}).$mount('#app')
</script>
```

#### vue.runtime.common.js

属于: 基于 CommonJS 的运行时构建
可以用于 Webpack-1 和 Browserify 之类打包工具
运行时构建不包含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组件中也依然可以写模板，因为单文件组件的模板会在构建时预编译为render函数, render函数的使用, 请参考: http://cn.vuejs.org/v2/guide/render-function.html

```js
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // tag name 标签名称
      this.$slots.default // 子组件中的阵列
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

#### vue.runtime.esm.js

属于: 基于 ES Module 的运行时构建
可以用于 Webpack-2 和 rollup 之类打包工具
运行时构建不包含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组件中也依然可以写模板，因为单文件组件的模板会在构建时预编译为render函数, render函数的使用, 请参考: http://cn.vuejs.org/v2/guide/render-function.html

### 示例同上。

#### vue.runtime.js

属于: 基于 UMD 的运行时构建
可以用于直接 CDN 引用
该版本和vue.js类似, 可以用于直接 CDN 引用, 因为不包含编译器, 所以不能使用template选项, 只能使用render函数

## 注意： 
* 默认 NPM 包导出的是 运行时 构建，因此在上述webpack中需要配置vue$别名

* .min.js 文件用于生成环境，删除了警告，关闭了调试


## 到底如何安装?

- 1. script 引入
此时： Vue会作为全局变量引入
```js
<script src="https://unpkg.com/vue/dist/vue.js"></script>  // 开发
//或
<script src="https://unpkg.com/vue/dist/vue.min.js"></script> //生产

```
- 2. npm 安装

$ npm install vue

结合webpack,rollup,vue-loader使用

具体可以查看官方文档，或

- 3. vue-cli

Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。该工具提供开箱即用的构建工具配置，带来现代化的前端开发流程。只需几分钟即可创建并启动一个带热重载、保存时静态检查以及可用于生产环境的构建配置的项目

```js
# 全局安装 vue-cli
$ npm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
# 安装依赖，走你
$ cd my-project
$ npm install
$ npm run dev
```

# 声明
为了便于演示，本书后续的所有代码，若无特别说明，均采用 vue-cli的方式搭建



