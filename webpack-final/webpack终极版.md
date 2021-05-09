# webpack讲解终极版



[TOC]

[手册网站，请点我](https://webpack.docschina.org/concepts/)

![image-20210509121339901](https://gitee.com/zyc1165472536/md-img/raw/master/20210509121341.png)

## 一：前瞻
### 1、什么是webpack

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 *bundle*。

### 2、为什么要使用webpack

![image-20210509133913947](https://gitee.com/zyc1165472536/md-img/raw/master/20210509133932.png)

![image-20210509134105957](https://gitee.com/zyc1165472536/md-img/raw/master/20210509134107.png)

> 刚开始学前端的时候，大家都知道前端分为html、css、js三部分，上图我写了一个less，浏览器是不支持不认识的，那我们为什么我们平常写代码能够正常运行，就是因为webpack构建项目的时候，把less处理成可识别的css文件在浏览器运行，举一反三如：typescript

webpack除了能够把一些特殊文件打包成浏览器可执行的文件输出，还有一些很让前端程序员头疼的事情，总所周知浏览器的内核分为多种如我们常见的谷歌、火狐、苹果浏览器的内核以及我们十分厌烦的IE内核，我们可以通过webpack的配置文件设置打包出来的包兼容多个浏览器，比如flex布局就是典型的问题。



node_module文件夹中常常会看到代码就一行紧凑的，是为什么呢？

> js是脚本语言，空格换行其实都是影响执行效率的，如果按照我们写的格式部署在服务器上，那么很影响网页的加载等，遇到一些执行大的代码段的时候就很影响用户体验了

为什么使用webpack原因有很多，等以后有需要用到的时候，就能慢慢的去体会理解了。

### 3、npm

这里稍微讲一下npm，不多说（自己也不是很清楚）

npm其实就是一个包管理器，类似于Java的maven，php的composer

npm是随同nodejs安装下载的

*拓展：个人现在安装的都是nvm和yarn

主要是讲一下npm的两块了解

> (1) package.json文件
>
> >![image-20210509140426214](https://gitee.com/zyc1165472536/md-img/raw/master/20210509140428.png)
> >
> >主要讲一下三个
> >
> >|      名称       |     解释     |
> >| :-------------: | :----------: |
> >|      name       |     包名     |
> >|     scripts     |   脚本设置   |
> >| devDependencies | 开发环境依赖 |
> >|  dependencies   | 生产环境依赖 |
> >
> >了解：
> >
> >devDependencies和dependencies
> >
> >安装模块的版本号符号含义
> >
> >^version中版本和小版本
> >
> >1.0.1-1.x.x
> >
> >~version小版本
> >
> >~1.0.1-1.0.x
> >
> >version 指定版本
>
> (2)npm常用指令
>
> > 安装/卸载
> >
> > #### 全局: 
> >
> > npm i -g  | npm install -g
> >
> > npm uninstall -g
> >
> > 
> >
> > #### 生产环境安装/卸载
> >
> > npm install module_name -S
> > npm install module_name --save 写入dependencies
> >
> > npm uninstall module_name -S
> > npm uninstall module_name --save 写入dependencies
> >
> > #### 开发环境安装/卸载
> > npm install module_name -D
> > npm install module_name --save-dev 写入devDependencies
> >
> > npm uninstall module_name -D
> > npm uninstall module_name --save-dev 写入devDependencies
> >
> > 

### 4、模块发展简讲

讲到**模块**可能想到的就是**模块化开发**

![image-20210509143401139](https://gitee.com/zyc1165472536/md-img/raw/master/20210509143402.png)

传统的上传方式是需要等待所有脚本加载完成，这个问题最大的弊端就是页面要等待，因为资源加载是同步的，所以会出先短暂的空白期，引入的脚本越多，时间越长，如果某一脚本加载失败，也可能直接挂掉，而模块化能够加速渲染。

接下来我们一步步的来说一下：

> 模块化为的是**高内聚低耦合**，每个模块之间各司其职。



这里我就不多说什么了，全局作用域和局部作用域，不了解的自行百度下。

以上图为例，引入多个script会存在的问题就是容易造成全局作用域冲突而导致系统崩溃

```
//a.js
const name = '小红';

// b.js
const name = '小绿';
```



前人就对代码进行了改进

```
//a.js
var red = {
	name:"小红"
	age:22
	tell:function(){
		console.log('我的名字叫'+this.name +'，我今年'+age)
	}
}

// b.js
var green = {
	name:"小绿"
	age:29
	tell:function(){
		console.log('我的名字叫'+this.name +'，我今年'+age)
	}
}
```

但是前人慢慢发现，这样的方式无法确保模块内部的的安全性，也能改掉属性值，所以就又进行改造，使用立即执行函数进行改写形成闭包。

```
(function(window){
	let name = "小红"
	let age = 26
	function tell(){
		console.log('我的名字叫'+this.name +'，我今年'+age)
	}
	window.redModue={tell}
})(window)
```

我们现在打开一个打包后的文件，或者node_module里的文件，基本都是运用了立即执行函数，Vue也是把自己挂载在了window全局作用于上。

拓展：AMD、CMD、CommonJs、ES6  [点击我](https://blog.csdn.net/tangxiujiang/article/details/81104174)

早期的模块化其实有很多，因为JS当初连类都没有，更别说模块了

像CMD、AMD 就是早年的产物，在一些老代码中还是能看到的

COMMONJS：这是nodejs的模块化，也是webpack配置文件中用到的

ES6：现在随着js发展的越来越好，目前最多最广的就是ES6的import和export



## 二：webpack的使用

### 1、Webpack的五个核心概念

随着webpack的不断更新迭代，现在的webpack已经结合了很多前人的习惯，指令输完已经有了很多默认配置好了，所以在一些项目中是看不到**webpack.config.js**文件了。这是其中一个原因（另一个可能就是不会配置）。

来先看看webpack的核心配置：↓↓↓↓↓↓↓↓

1. Entry 

   > 入口(Entry)指示 webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图。

2. Output

   > 输出(Output)指示 webpack 打包后的资源 bundles 输出到哪里去，以及如何命名。

3. Loader

   > Loader 让 webpack 能 够 去 处 理 那 些 非 JavaScript 文 件 (webpack 自 身 只 理 解 JavaScript) 

4. Plugins

   > 插件(Plugins)可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩， 一直到重新定义环境中的变量等。 

5. Mode

   > 模式(Mode)指示 webpack 使用相应模式的配置
   >
   > |         选项         |                             描述                             |            特点             |
   > | :------------------: | :----------------------------------------------------------: | :-------------------------: |
   > | development 开发模式 | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为		  development。启用 NamedChunksPlugin和NamedModulesPlugin。 | 能让代码本地调试 运行的环境 |
   > | production 生产模式  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin | 能让代码优化上线 运行的环境 |
	> 为什么要有两套配置文件呢？
	>
	> 试想一下你开发的时候，开发者模式打开，程序出错的情况下，有报错信息和分析舒服还是没有舒服；Vue的警告也很多，如果你是用户，不小心点开了之后看到一堆黄色警告是否舒服，作为开发人员，如果一堆换色警告说明代码其实是有风险的，你能即使更改，降低bug。

### 2、开发环境配置
```
/*
  开发环境配置：能让代码运行
    运行项目指令：
      webpack 会将打包结果输出出去
      npx webpack-dev-server 只会在内存中编译打包，没有输出
*/

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      // loader的配置
      {
        // 处理less资源
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        // 处理css资源
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        // 处理图片资源
        test: /\.(jpg|png|gif)$/,
        loader: 'url-loader',
        options: {
          limit: 8 * 1024,
          name: '[hash:10].[ext]',
          // 关闭es6模块化
          esModule: false,
          outputPath: 'imgs'
        }
      },
      {
        // 处理html中img资源
        test: /\.html$/,
        loader: 'html-loader'
      },
      {
        // 处理其他资源
        exclude: /\.(html|js|css|less|jpg|png|gif)/,
        loader: 'file-loader',
        options: {
          name: '[hash:10].[ext]',
          outputPath: 'media'
        }
      }
    ]
  },
  plugins: [
    // plugins的配置
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development',
  devServer: {
    contentBase: resolve(__dirname, 'build'),
    compress: true,
    port: 3000,
    open: true
  }
};
```

### 2、生产环境配置
```
const { resolve } = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

// 定义nodejs环境变量：决定使用browserslist的哪个环境
process.env.NODE_ENV = 'production';

// 复用loader
const commonCssLoader = [
  MiniCssExtractPlugin.loader,
  'css-loader',
  {
    // 还需要在package.json中定义browserslist
    loader: 'postcss-loader',
    options: {
      ident: 'postcss',
      plugins: () => [require('postcss-preset-env')()]
    }
  }
];

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [...commonCssLoader]
      },
      {
        test: /\.less$/,
        use: [...commonCssLoader, 'less-loader']
      },
      /*
        正常来讲，一个文件只能被一个loader处理。
        当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序：
          先执行eslint 在执行babel
      */
      {
        // 在package.json中eslintConfig --> airbnb
        test: /\.js$/,
        exclude: /node_modules/,
        // 优先执行
        enforce: 'pre',
        loader: 'eslint-loader',
        options: {
          fix: true
        }
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          presets: [
            [
              '@babel/preset-env',
              {
                useBuiltIns: 'usage',
                corejs: {version: 3},
                targets: {
                  chrome: '60',
                  firefox: '50'
                }
              }
            ]
          ]
        }
      },
      {
        test: /\.(jpg|png|gif)/,
        loader: 'url-loader',
        options: {
          limit: 8 * 1024,
          name: '[hash:10].[ext]',
          outputPath: 'imgs',
          esModule: false
        }
      },
      {
        test: /\.html$/,
        loader: 'html-loader'
      },
      {
        exclude: /\.(js|css|less|html|jpg|png|gif)/,
        loader: 'file-loader',
        options: {
          outputPath: 'media'
        }
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'css/built.css'
    }),
    new OptimizeCssAssetsWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: {
        collapseWhitespace: true,
        removeComments: true
      }
    })
  ],
  mode: 'production'
};
```

## 三：vue.config.js

```
const path = require('path')
const CompressionPlugin = require("compression-webpack-plugin")

function resolve(dir) {
  return path.join(__dirname, dir)
}

// vue.config.js
module.exports = {
  /*
    Vue-cli3:
    Crashed when using Webpack `import()` #2463
    https://github.com/vuejs/vue-cli/issues/2463
   */
  // 如果你不需要生产环境的 source map，可以将其设置为 false 以加速生产环境构建。
  productionSourceMap: false,
  //打包app时放开该配置
  //publicPath:'./',
  configureWebpack: config => {
    //生产环境取消 console.log
    if (process.env.NODE_ENV === 'production') {
      config.optimization.minimizer[0].options.terserOptions.compress.drop_console = true
    }
  },
  chainWebpack: (config) => {
    config.resolve.alias
      .set('@$', resolve('src'))
      .set('@api', resolve('src/api'))
      .set('@assets', resolve('src/assets'))
      .set('@comp', resolve('src/components'))
      .set('@views', resolve('src/views'))

    //生产环境，开启js\css压缩
    if (process.env.NODE_ENV === 'production') {
        config.plugin('compressionPlugin').use(new CompressionPlugin({
          test: /\.(js|css|less)$/, // 匹配文件名
          threshold: 10240, // 对超过10k的数据压缩
          deleteOriginalAssets: false // 不删除源文件
        }))
    }

    // 配置 webpack 识别 markdown 为普通的文件
    config.module
      .rule('markdown')
      .test(/\.md$/)
      .use()
      .loader('file-loader')
      .end()

    // 编译vxe-table包里的es6代码，解决IE11兼容问题
    config.module
      .rule('vxe')
      .test(/\.js$/)
      .include
        .add(resolve('node_modules/vxe-table'))
        .add(resolve('node_modules/vxe-table-plugin-antd'))
        .end()
      .use()
      .loader('babel-loader')
      .end()

  },

  css: {
    loaderOptions: {
      less: {
        modifyVars: {
          /* less 变量覆盖，用于自定义 ant design 主题 */
          'primary-color': '#1890FF',
          'link-color': '#1890FF',
          'border-radius-base': '4px',
        },
        javascriptEnabled: true,
      }
    }
  },

  devServer: {
    port: 3000,
    proxy: {
     /* '/api': {
        target: 'https://mock.ihx.me/mock/5baf3052f7da7e07e04a5116/antd-pro', //mock API接口系统
        ws: false,
        changeOrigin: true,
        pathRewrite: {
          '/jeecg-boot': ''  //默认所有请求都加了jeecg-boot前缀，需要去掉
        }
      },*/
      '/jeecg-boot': {
        target: 'http://localhost:8080', //请求本地 需要jeecg-boot后台项目
        ws: false,
        changeOrigin: true
      },
    }
  },

  lintOnSave: undefined
}
```




## 四：webpack与typescript





