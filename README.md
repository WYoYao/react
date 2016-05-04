# 1.创建项目


### 创建文件夹,初始化NPM,初始化GIT
```
$ mkdir webpack-project && cd webpack-project
$ git init
$ npm init
$ touch .gitignore
```
### 代码清单
```
node_modules/
.idea/
.project
*.log*
```
### 创建两个目录
```
$ mkdir app public
```
> 代码清单：
> app/index.js
> alert('hello world!');
> public/index.html
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>React Demo</title>
</head>
<body>
  <div id="app"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

# 2.安装webpack和webpack-dev-server
> 给项目添加工具依赖，后面把命名行配置在scripts中
> (--save 保存到生产环境下package.json 的dependencies 中)
> (--save-dev 保存到开发环境下package.json 的devDependencies 中)
```
$ npm install webpack webpack-dev-server --save-dev
```
> 全局安装 webpack-dev-server
> webpack-dev-server是一个小型的Express服务器,它使用webpack-dev-middleware中间件来为通过webpack打包生成的资源文件提供Web服务.
> 需要在项目根目录下创建webpack.config.js文件
```
$ touch webpack.config.js
```
> 代码清单：webpack.config.js
```
var path = require('path');

module.exports = {
    entry: path.resolve(__dirname, 'app/index.js'),
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'bundle.js',
    }
};
```

> 运行下面的命令
```
- webpack 开发环境下编译
- webpack -p 产品编译及压缩
- webpack --watch 开发环境下持续的监听文件变动来进行编译(非常快!)
- webpack -d 引入 source maps
- webpack --progress 显示构建进度
- webpack --display-error-details 这个很有用，显示打包过程中的出错信息
```

> webpack-dev-server来起一个本地服务进行调试
```
$ webpack-dev-server --progress --colors --content-base public
```

# 3.基于React + ES6的基本代码体验
### 在app/index.js里面尝试写一个最基本的组件代码，
> 暂时不用理会代码为什么要这么写，这里先把ES6语法和JSX语法加进来，用于跑通我们的开发环境.
> 下面的代码用到了基本的react.js和react-dom.js，而且使用的是ES6的语法来封装的组件和应用模块。
> 第一点：下载相应的模块供其调用
```
$ npm install --save react react-dom
```
> 下载并配置babel，以解析ES6语法和JSX语法。

> 先全局安装babel-cli以方便运行babel命令和babel-node命令
```
$ npm install babel-cli -g
$ npm install babel-loader babel-core --save-dev
```

```
'use strict';

import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class HelloWorld extends Component {
  render(){
    return (
      <h1>Hello world</h1>
    )
  }
}

ReactDOM.render(<HelloWorld />, document.getElementById('app'));
```

### 配置webapck.config.js文件
> 代码清单：webpack.config.js
> 指定了使用babel-loader来解析js文件，但是并没有告诉babel应该如何来解析，所以我们> > 需要创建一个babelrc配置文件

```
var path = require('path');

module.exports = {
    entry: path.resolve(__dirname, 'app/index.js'),
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'bundle.js',
    },
    module: {
      loaders: [
        {
          test: /\.js$/,
          loader: 'babel-loader'
        }
      ]
    }
};
```
### 配置.babelrc 文件

```
$ touch .babelrc
```
> 代码清单：.babelrc
> 下面两个参数的意义
>  presets 为babel 的解析做预设,配置babel 需要用哪些插件来解析代码
>  plugins 配置使用babel相关的插件的
```
{
  "presets": ["es2015", "react", "stage-0"],
  "plugins": []
}
```
> 上面分别用到了三个预设"es2015", "react", "stage-0" (stage-0预设是用来说明解析ES7其中一个阶段语法提案的转码规则)
> 接下来对它们进行加载
```
$ npm install --save-dev es2015 react stage-0
```

# 4.多个入口文件
> 你有两个页面：profile和feed。如果你希望用户访问profile页面时不加载feed页面的代码，那就需要生成多个bundles文件：为每个页面创建自己的“main module”（入口文件）。
```
// webpack.config.js
module.exports = {
  entry: {
    Profile: './profile.js',
    Feed: './feed.js'
  },
  output: {
    path: 'build',
    filename: '[name].js' // name是基于上边entry中定义的key
  }
};
```
> 在profile页面中插入<script src="build/Profile.js"></script>。feed也一样。

# 5.实现代码热替换
> webpack.config.js更新一下入口文件配置即可实现编辑器中保存代码就可在浏览器中实现刷新的效果.
> 清单：webpack.config.js
```
entry: [
      'webpack-dev-server/client?http://localhost:8080',
      path.resolve(__dirname, 'app/index.js')
]
```

# 6.使用react-hot-loader实现组件级的hot reload
> 实现了代码的热替换，只要在编辑器中保存我们编辑的代码，浏览器即可实时刷新。但同时也有一个烦恼，
> 如果我们的项目开发中用到了几十个组件，为了测试某个组件我们需要一步步操作到固定的步骤去实现，
> 一旦保存编辑器中修改的一行代码，从入口文件开始的所有代码都全部刷新了一次，这样很不利于调试。
> react-hot-loader 可以帮我们解决这样一个问题
```
$ npm install --save-dev react-hot-loader
```
> 然后重新配置webpack.config.js。		 代码清单：webpack.config.js

```
var path = require('path');
var webpack = require('webpack');

module.exports = {
    entry: [
      'webpack/hot/dev-server',
      'webpack-dev-server/client?http://localhost:8080',
      path.resolve(__dirname, 'app/index.js')
    ],
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'bundle.js',
    },
    module: {
      loaders: [
        {
          test: /\.js$/,
          loaders: ['react-hot', 'babel'],
          exclude: path.resolve(__dirname, 'node_modules')
        }
      ]
    },
    plugins: [
      new webpack.HotModuleReplacementPlugin(),
      new webpack.NoErrorsPlugin()
    ]
};
```
> 这里新增了react-hot-loader去解析React组件，同时加上了热替换的插件HotModuleReplacementPlugin和防止报错的插件NoErrorsPlugin，
> 如果这里不用HotModuleReplacementPlugin这个插件也可以，只需要在webpack-dev-server运行的时候加上--hot这个参数即可，
> 都是一样的。
# 7.加载并解析样式文件
> 前面的大部分工作都在处理JS逻辑的解析和加载，但是我们还一直没有提我们的样式文件应该如何去处理。
> 我们在课程中暂且约定使用less预处理器（saas的类似）来写我们项目的样式，那么首先需要下载几个webpack的

```
$ npm install --save-dev style-loader css-loader less-loader
```
