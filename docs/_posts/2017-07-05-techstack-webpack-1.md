---
layout: post
title: 关于Webpack中一些让人困惑的地方的解答
date: '2017-07-05 10:10'
categories: techstack
permalink: /techstack/webpack/1
---

> 原文连接: [Webpack -- The Confusing Parts](https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9)

Webpack是React和Redux项目的主要模块加载器。我认为使用Angular2和其他的框架的人在如今也大量使用Webpack进行开发。

当我第一次查看Webpack的配置文件时，我是懵逼的。在使用过一段时间以后，我觉得这是因为Webpack有着独一无二的语法和标新立异的哲学思想，所以在刚开始使用的时候可能会造成一定的困惑。凑巧的是，这些哲学思想也是让它如此受欢迎的原因。

正因为Webpack的起步比较容易产生困惑，所以我希望写一些什么出来，好让更多人更容易上手并且体验它强大的特性。

接下来是第一部分。

# Webpack的核心哲学思想

两个核心哲学思想是：

1. 一切都是模块----就像JS文件可以视作"模块"一样，其他所有的一切（CSS，图片，HTML）都可以被视作模块。也就是说，你可以`require("myJSfile.js")`或者`require("myCSSfile.css")`。这意味着我们可以把任何静态资源分割成可控的模块，以供重复使用等不同的操作。
2. 只加载"你需要的"和"你何时需要"的----典型的模块加载器会把所有的模块最终打包生成一个巨大的"bundle.js"文件。但在很多实际的项目当中，这个"bundle.js"文件体积可能会达到10MB~15MB，并且会一直不停进行加载！所以Webpack通过大量的特性去分割你的代码，生成多个"bundle"片段，并且异步地加载项目的不同部分，因此只会为你加载"你需要的"和"你何时需要"的部分。

OK，让我们一起来看看那些"让人困惑"的部分吧。

# 1\. 开发环境 VS 生产环境

第一件需要意识到的事情是Webpack拥有着大量的特性。有一些是"开发环境专用"的，一些是"生产环境专用"的，还有一些是"通用"的。 ![](http://os15c15vv.bkt.clouddn.com/webpack_1.png)

> 一般来说，大部分的项目都使用了许多Webpack的特性，所以它们通常有两个大的`webpack config`文件，用于区分开发环境和生产环境。

# 2\. webpack CLI Vs webpack-dev-server

明白Webpack这个模块加载器拥有两个接口是非常重要的：

1. Webpack CLI tool ----默认的接口（和Webpack一并被安装）
2. webpack-dev-server tool ----这个工具通过来自CLI和配置文件（默认：`webpack.config.js`）的配置项来控制Webpack的打包动作。

> 你刚开始学习Webpack的时候可能是从CLI入手的，但你接下来很可能只会用它去建立生产环境的项目。

使用方法：

```
OPTION 1:
//全局安装
npm install webpack --g

//在终端使用
$ webpack //<--通过webpack.bundle.js进行打包

OPTION 2 :
//本地安装并写入package.json依赖
npm install webpack --save

//添加到package.json的script内
"scripts": {
    "build": "webpack --config webpack.config.prod.js -p",
    ...
}

//开始构建
npm run build
```

## Webpack-dev-server (有利于开发环境使用)

这是一个运行在8080端口的基于Express的node.js服务器。这个服务器会在内部调用Webpack。它的优势是提供了额外的能力----类似可以刷新浏览器的"Live Reloading"，以及（或者）局部更新模块的"模块热重载"功能（HMR）。

使用方法：

```
OPTION 1:
//全局安装
npm install webpack-dev-server --save

//终端使用
$ webpack-dev-server --inline --hot

OPTION 2:
//添加到package.json的script内
"scripts": {
    "start": "webpack-dev-server --inline --hot",
    ...
}

//输入下列命令行进行使用
$ npm start

浏览器打开下列地址
http://localhost:8080
```

## Webpack Vs webpack-dev-server options

值得注意的是，有一些选项比如"inline"和"hot"仅用于webpack-dev-server，而比如"hide-modules"仅用于CLI。

## webpack-dev-server CLI options Vs config options

另外一件需要知道的事情是你可以通过两种方式对webpack-dev-server进行配置：

1. 通过`webpack.config.js`的"devServer"对象。
2. 通过CLI选项。

```
//使用CLI
webpack-dev-server --hot --inline

//使用webpack.config.js
devServer: {
    inline: true,
    hot:true
}
```

> 我发现有时候devServer的配置并不管用！所以我更倾向于把这些选项以CLI的方式写入package.json里面：

```
//package.json
{
    scripts:{
        "start": "webpack-dev-server --hot --inline"
    }
}
```

> 注意：确保你木有把hot:true和-hot写在一块儿。

## "hot" Vs "inline" webpack-dev-server options

"inline"选项为整个页面提供了"Live reloading"功能。"hot"选项提供了"模块热重载"功能，它会尝试仅仅更新组件被改变的部分（而不是整个页面）。如果我们把这两个选项都写上，那么当文件被改动时，webpack-dev-server会先尝试HMR，如果这不管用，它就会重新加载整个页面。

```
//当文件被改动后，下面的三个选项都会生成新的bundle，但是，

//1\. 页面不会刷新
$ webpack-dev-server

//2\. 刷新整个页面
$ webpack-dev-server --inline

//3\. 仅仅刷新被改动的部分（HMR），如果HMR失败则刷新整个页面
$ webpack-dev-server  --inline --hot
```

# 3."entry"----字符串VS数组VS对象

entry告诉Webpack入口文件或者起点在哪里。它可以是一个字符串，一个数组或者一个对象。这可能会使你感到困惑，但不同的类型适用于不同的场合。

如果你使用的是单个起点（大部分项目都是如此），那么你可以使用任意的类型，它们的结果都会是一样的。 ![](http://os15c15vv.bkt.clouddn.com/webpack_2.png)

## entry----数组

但是，如果你想要添加互不依赖的多个文件，你可以使用数组的格式。

举个栗子，你的HTML可能需要"googleAnalytics.js"。你可以告诉Webpack在bundle.js的后面把它添加进去： ![](http://os15c15vv.bkt.clouddn.com/webpack_3.png)

## entry----对象

现在，当你有一个包含多个HTML文件的多页应用，而不是单页应用的项目的时候（index.html和profile.html），你可以通过对象格式告诉Webpack去一次性生成多个bundle文件。

下面的配置会生成两个JS文件：`indexEntry.js`和`profileEntry.js`，你可以在`index.html`和`profile.html`分别使用它们： ![](http://os15c15vv.bkt.clouddn.com/webpack_4.png) 使用方法：

```
//profile.html
<script src=”dist/profileEntry.js”></script>

//index.html
<script src=”dist/indexEntry.js”></script>
```

> 注意：文件名来自"entry"对象的key。

## entry----组合格式

你也可以在entry对象中使用数组。下面的例子会生成三个文件：一个包含三个文件的`vendor.js`，一个`index.js`和一个`profile.js`。 ![](http://os15c15vv.bkt.clouddn.com/webpack_5.png)

# 4\. output -- "path" Vs "publicPath"

output告诉Webpack应该在哪里以怎样的方式去放置打包好的文件。它有两个属性："path"和"publicPath"，这也许会对用户造成一定的困惑。

"path"会简单地告诉Webpack生成文件输出位置。"publicPath"多被一些Webpack的插件使用，在HTML文件以生产环境方式被构建的时候，更新CSS文件内的URL地址。 ![](http://os15c15vv.bkt.clouddn.com/webpack_6.png) 举个栗子，在你的CSS文件里面，你可能会在URL里面加载`./test.png`。但是在生产环境中，`test.png`很可能放在CDN内----比如当你的node.js服务器运行在Heroku的时候。这意味着，你可能在生产环境内不得不手动更新文件内的URL指向。

相反，你可以使用Webpack的`publicPath`以及其他适用于这个属性的插件在生产环境中自动地更新文件内部的URL指向。 ![](http://os15c15vv.bkt.clouddn.com/webpack_7.png)

```
//开发环境：服务器和图片都放在本地
.image {
    background-image: url(‘./test.png’);
}

//生产环境：服务器在Heroku而图片在CDN
.image {
    background-image: url(‘https://someCDN/test.png’);
}
```

# 5\. 加载器和链式加载器

加载器是额外的node模块，用于"加载"或者"引入"不同类型的文件，并把他们转化成浏览器能够识别的格式----比如JS文件、内联样式表或其他格式。另外，加载器也允许以"require"或者ES6的"import"的方式把这些文件引入到JS文件当中。

例如，你可以使用`babel-loader`把ES6代码转化成ES5代码：

```
module: {
    loaders: [{
        test: /\.js$/, // 判断文件格式，若为“.js”文件则调用loader
        exclude: /node_modules/, // 排除node_modules文件夹
        loader: 'babel' // 使用babel（babel-loader的缩写）
    }]
}
```

## 链式加载器（从右到左的顺序进行工作）

不同的加载器可以链式地在针对同一个文件类型进行工作。链式加载器的工作顺序是从右到左的，并且通过"！"分割。

举个栗子，我们有一个叫做`myCssFile.css`的CSS文件，现在想把它以`<style></style>`的方式在我们的HTML文件中使用，可以通过两个加载器去完成这个需求：`css-loader`和`style-loader`。

```
module: {
    loaders: [{
        test: /\.css$/,
        loader: ‘style!css’ // style-loader!css-loader的缩写
    }]
}
```

这是运行原理： ![](http://os15c15vv.bkt.clouddn.com/webpack_8.png)

1. Webpack搜寻被模块所引用的CSS文件。意思是Webpack会检查一个JS文件内是否有`require(myCssFile.css)`，如果有这句话并且找到了这个依赖，它会首先把这个文件交给`css-loader`。
2. `css-loader`加载所有的CSS文件及其依赖包（例如通过`@import`引入的其他CSS文件）到一个JSON文件中。随后Webpack会把结果交给`style-loader`。
3. `style-loader`拿到这个JSON文件并把它注入到`<style></style>`标签当中，并把这个标签添加到`index.html`文件内。

# 6.加载器自身是可配置的

加载器其自身可以通过配置不同的参数实现不同的功能。

在下面的例子中，我们配置了`url-loader`，当图片小于1024byte的时候使用DataURL，当图片大雨1024byte的时候使用URL。我们通过下面两个传入`limit`参数的方法来实现这个功能： ![](http://os15c15vv.bkt.clouddn.com/webpack_9.png)

# 7\. babelrc文件

`babel-loader`使用`presets`去规定如何把ES6代码转化为ES5代码，以及如何把React的JSX转化为JS。我们可以通过`query`方法进行配置：

```
module: {
    loaders: [{
        test: /\.jsx?$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel',
        query: {
            presets: ['react', 'es2015']
        }
    }]
}
```

然而，在许多项目中babel的配置项会非常巨大。所以作为替代，你可以把这些配置项写入一个叫做`.babelrc`的文件中。如果这个文件存在的话`bable-loader`会自动的加载这个文件。

所以在许多例子中，你会看到：

```
//webpack.config.js
module: {
    loaders: [{
        test: /\.jsx?$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel'
    }]
}

//.bablerc
{
    “presets”: [“react”, “es2015”]
}
```

# 8\. 插件

插件是额外的node模块，多用于处理输出文件。

例如，`uglifyJSPlugin`会压缩并混淆JS代码，使其体积减小。

同样的，`extract-text-webpack-plugin`会在内部使用`css-loader`和`style-loader`去把所有的CSS合并为一个文件，并且最终把结果提取到一个分离在外部的`style.css`文件中，最后在`index.html`中引用这个CSS文件。

```
//webpack.config.js
//Take all the .css files, combine their contents and it extract them to a single "styles.css"
var ETP = require("extract-text-webpack-plugin");

module: {
    loaders: [{
        test: /\.css$/, loader:ETP.extract("style-loader","css-loader")
    }]
},
plugins: [
    new ExtractTextPlugin("styles.css") //Extract to styles.css file
]
```

注意，如果你只打算把CSS以行内样式的形式在HTML中引用，你可以仅仅使用`css-loader`和`style-loader`，像下面的例子：

```
module: {
    loaders: [{
        test: /\.css$/,
        loader: ‘style!css’ // style-loader!css-loader的缩写
    }]
}
```

# 9\. 加载器 VS 插件

正如你可能已经弄明白的，加载器在单个文件的程度上，在打包结束之前或者打包的过程中运行。

而插件是在打包或者数据块的程度上，在输出打包文件的过程中进行运作。一些插件比如commonsChunksPlugins甚至在更早的阶段开始运作，可以用来修改打包的方式。

# 10\. 解析文件扩展名

一些Webpack配置文件带有解析扩展文件名的属性，它们像下面的例子一样，包含了一些空字符串。这些空字符串被用于帮助加载一些没有扩展名的文件，比如`require("./myJSFile")`或者`import myJSFile from './myJSFile'`。

```
{
    resolve: {
        extensions: ['', '.js', '.jsx']
    }
}
```
