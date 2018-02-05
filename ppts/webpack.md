title: webpack
speaker: jjk
url: localhost
files: /js/demo.js,/css/demo.css,/js/zoom.js
[slide]
# webpack
## 概念
### 本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

## 优势
### 高度可配置
1. webpack 是以 commonJS 的形式来书写脚本滴，但对 AMD/CMD 的支持也很全面，方便旧项目进行代码迁移。
2. 能被模块化的不仅仅是 JS 了。
3. 开发便捷，能替代部分 grunt/gulp 的工作，比如打包、压缩混淆、图片转base64等。
4. 扩展性强，插件机制完善


[slide]
## webpack执行顺序

1. 从入口文件开始递归地建立一个依赖关系图。
2. 把所有文件都转化成模块函数。
3. 根据依赖关系，按照配置文件把模块函数分组打包成若干个bundle。
4. 通过script标签把打包的bundle注入到html中，通过manifest文件来管理bundle文件的运行和加载。

### 打包的规则为：一个入口文件对应一个bundle。该bundle包括入口文件模块和其依赖的模块。按需加载的模块或需单独加载的模块则分开打包成其他的bundle。

### 除了这些bundle外，还有一个特别重要的bundle，就是manifest.bundle.js文件，即webpackBootstrap。这个manifest文件是最先加载的，负责解析webpack打包的其他bundle文件，使其按要求进行加载和执行。

[slide]
## webpack 使用

### 安装
npm webpack 

### 使用

####使用配置文件的用法

```
webpack [--config webpack.config.js]
```

```
webpack <entry> [<entry>] <output>
```

ps:默认使用当前文件夹下的webpack.config.js 文件

[slide]
## 入口起点(Entry Points)
```
entry: string|Array<string>
```

## 输出(Output)
### 用法
#### 单个入口
```
const config = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};

module.exports = config;
```

#### 多个入口
```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}

// 写入到硬盘：./dist/app.js, ./dist/search.js
```

[slide]
## Loader
### loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！

### 使用loader
* 配置（推荐）：在 webpack.config.js 文件中指定 loader。
* 内联：在每个 import 语句中显式指定 loader。
* CLI：在 shell 命令中指定它们。

[slide]
### 配置[Configuration]

module.rules 允许你在 webpack 配置中指定多个 loader。 这是展示 loader 的一种简明方式，并且有助于使代码变得简洁。同时让你对各个 loader 有个全局概览：

```
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
```


### 内联

可以在 import 语句或任何等效于 "import" 的方式中指定 loader。使用 ! 将资源中的 loader 分开。分开的每个部分都相对于当前目录解析。

```
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

### cli

你也可以通过 CLI 使用 loader：
```
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```

[slide]
### Loader 特性
* loader 支持链式传递。能够对资源使用流水线(pipeline)。一组链式的 loader 将按照相反的顺序执行。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
* loader 可以是同步的，也可以是异步的。
* loader 运行在 Node.js 中，并且能够执行任何可能的操作。
* loader 接收查询参数。用于对 loader 传递配置。
* loader 也能够使用 options 对象进行配置。
* 除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段。
* 插件(plugin)可以为 loader 带来更多特性。
* loader 能够产生额外的任意文件。

[slide]

## 插件(Plugins)

插件是 wepback 的支柱功能。webpack 自身也是构建于，你在 webpack 配置中用到的相同的插件系统之上！
插件目的在于解决 loader 无法实现的其他事。

### 剖析

webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。

### 用法

由于插件可以携带参数/选项，你必须在 webpack 配置中，向 plugins 属性传入 new 实例。
根据你的 webpack 用法，这里有多种方式使用插件。

[slide]
### 配置

webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const webpack = require('webpack'); //访问内置的插件
const path = require('path');

const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    loaders: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader'
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

[slide]
### Node API
ps:即便使用 Node API，用户也应该在配置中传入 plugins 属性。compiler.apply 并不是推荐的使用方式

some-node-script.js
```
const webpack = require('webpack'); //访问 webpack 运行时(runtime)
  const configuration = require('./webpack.config.js');

  let compiler = webpack(configuration);
  compiler.apply(new webpack.ProgressPlugin());

  compiler.run(function(err, stats) {
    // ...
  });

```

[slide]
## 模块解析(Module Resolution)

### 绝对路径
```
import "/home/me/file";

import "C:\\Users\\me\\file";
```

### 相对路径

```
import "../src/file1";
import "./file2";
```

### 模块路径
```
import "module";
import "module/lib/file";
```

[slide]
## 构建目标(Targets)

因为服务器和浏览器代码都可以用 JavaScript 编写，所以 webpack 提供了多种构建目标(target)，你可以在你的 webpack 配置中设置。

### 用法

要设置 target 属性，只需要在你的 webpack 配置中设置 target 的值。
webpack.config.js
```
module.exports = {
  target: 'node'
};
```

ps:在上面例子中，使用 node webpack 会编译为用于「类 Node.js」环境（使用 Node.js 的 require ，而不是使用任意内置模块（如 fs 或 path）来加载 chunk）。

[slide]

## 多个 Target
尽管 webpack 不支持向 target 传入多个字符串，你可以通过打包两份分离的配置来创建同构的库：
webpack.config.js
```
var path = require('path');
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};

var clientConfig = {
  target: 'web', // <=== 默认是 'web'，可省略
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];
```

[slide]
## 传入funciton
例如，如果你不需要使用以上任何插件：
```
const options = {
  target: () => undefined
};
```
或者可以使用你想要指定的插件
```
const webpack = require("webpack");

const options = {
  target: (compiler) => {
    compiler.apply(
      new webpack.JsonpTemplatePlugin(options.output),
      new webpack.LoaderTargetPlugin("web")
    );
  }
};
```



## Manifest
webpack manifest文件用来引导所有模块的交互。manifest文件包含了加载和处理模块的逻辑。

当webpack编译器处理和映射应用代码时，它把模块的详细的信息都记录到了manifest文件中。当模块被打包并运输到浏览器上时，runtime就会根据manifest文件来处理和加载模块。利用manifest就知道从哪里去获取模块代码。