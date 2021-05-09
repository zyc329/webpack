# 一、webpack

[手册网站，请点我](https://webpack.docschina.org/concepts/)

## 1、WebPack是什么

![image-20210427105719574](https://gitee.com/zyc1165472536/md-img/raw/master/image-20210427105719574.png)

```
webpack是一种前端资源构建工具，一个静态模块打包器，在webpack看来，前端所有的资源文件（js/json/css/img/less/...）都会作为模块处理。它讲根据木块的以来关系进行静态分析，打包成对应的静态资源（bundle）
```

### 2、Webpack的五个核心概念

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
   > | 选项 |描述 |特点|
   > | :--: | :--: | :--: |
   > | development 开发模式 | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为		  development。启用 NamedChunksPlugin和NamedModulesPlugin。 |能让代码本地调试 运行的环境 |
   > | production 生产模式 |会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin|能让代码优化上线 运行的环境|
   >

## 3、初体验

- webpack环境指令

  > npm init  //主要是生成packing.json文件
  >
  > npm i webpack webpack-cli -g  //下载webpack的代码
  >
  > npm i webpack webpack-cli -D //下载为项目的开发依赖 

- 配合代码学习 1.webpack基本使用/02.webpack初体验

  *稍微记住一下Hash*
```
以默认开发环境
1：webpack ./src/index.js -o ./build/built.js --mode=development
以默认生成环境
2：webpack ./src/index.js -o ./build/built.js --mode=production
```



## 4、webpack.config.js

- 什么是webpack.config.js

> webpack的配置文件
> 作用: 指示 webpack 干哪些活（当你运行 webpack 指令时，会加载里面的配置）
> 所有构建工具都是基于nodejs平台运行的~模块化默认采用commonjs。
>
> > 拓展：
> > AMD、CMD、CommonJs、ES6：  [点击我](https://blog.csdn.net/tangxiujiang/article/details/81104174)

- 最简单的webpack.config.js格式

```
const { resolve } = require('path');

module.exports = {
  // webpack配置
  // 入口起点
  entry: './src/index.js',
  // 输出
  output: {
    // 输出文件名
    filename: 'built.js',
    // 输出路径
    // __dirname nodejs的变量，代表当前文件的目录绝对路径
    path: resolve(__dirname, 'build')
  },
  // loader的配置
  module: {
    rules: [
      // 详细loader配置
      // 不同文件必须配置不同loader处理
      {
        // 匹配哪些文件
        test: /\.css$/,
        // 使用哪些loader进行处理
        use: [
          // use数组中loader执行顺序：从右到左，从下到上 依次执行
          // 创建style标签，将js中的样式资源插入进行，添加到head中生效
          'style-loader',
          // 将css文件变成commonjs模块加载js中，里面内容是样式字符串
          'css-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          // 将less文件编译成css文件
          // 需要下载 less-loader和less
          'less-loader'
        ]
      }
    ]
  },
  // plugins的配置
  plugins: [
    // 详细plugins的配置
  ],
  // 模式
  mode: 'development', // 开发模式
  // mode: 'production'
}
```

- 有了webpack.config.js文件后直接执行指令 webpack就能读取该配置信息来执行打包











# 二、实战 Webpack + TypeScript



