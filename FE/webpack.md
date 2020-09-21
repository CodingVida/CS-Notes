[TOC]

### 1. 为什么需要构建工具

* 转换ES语法
* css 预处理
* 压缩混淆
* 图片压缩

### 2. 前端构建演变之路

* YUI Dev tools
* grunt
* gulp
* webpack、rollup、parcel

### 3. 为什么选择webpack

* 社区生态繁荣
* 配置灵活、插件化拓展
* 更新迭代速度快

### 4. 初识webpack

* 配置化：

    * webpack.config.js
    * webpack --config xxx.js

* 配置组成

    * 入口：entry

    * 输出：output

    * loader配置

        ```javascript
        ...
        module: {
            rules: [
                { test: '/\.txt$/', use: 'raw-loader' }
            ]
        }
        ...
        ```

    * plugins配置

        ```javascript
        ...
        plugins: [
            new HtmlwebpackPlugin({
                template: './src/index.html'
            })
        ]
        ...
        ```



### 4. 核心概念 -- entry

> 打包入口

* 单入口

    * 字符串

    ```javascript
    module.exports = {
        entry: './path/to/my/entry/file.js'
    }
    ```

    

* 多入口

    * 对象

    ```JavaScript
    module.exports = {
        entry: {
            app: './src/app.js',
            adminApp: './src/adminApp.js'
        }
    }
    ```

    

### 5. 核心概念 -- output

* 单入口

    ```javascript
    module.exports = {
        entry: '/path/to/my/entry/file.js',
        output: {
            filename: 'bundle.js',
            path: __dirname + '/dist'
        }
    }
    ```

    

* 多入口

    ```javascript
    module.exports = {
        entry: {
            app: '/path/app.js',
            adminApp: '/path/adminApp.js'
        },
        output: {
            filename: '[name].js', 	// 通过占位符确保文件名称唯一
            path: __dirname + '/dist'
        }
    }
    ```

    

### 6. 核心概念 -- loader

> webpack 开箱即用只支持js和json两种文件类型，需要通过loader将其他类型转换为有效的模块，从而可以添加到依赖图中。
>
> loader本身是一个函数，接收源文件作为参数，返回转换的结果。

```javascript
module.exports = {
    ...
    module: {
        rules: [
            { test: '/\.txt$/', use: 'raw-loader' }
        ]
    }
}
```

* test: 指定匹配规则
* use：指定使用的 loader 名称

### 7. 核心概念 -- plugins

> 插件用于 **bundle文件的优化**、**资源管理** 和 **环境变量** 的注入。
>
> 作用于整个构建过程。

常见的plugins：

| 名称                     | 描述                                       |
| ------------------------ | ------------------------------------------ |
| CommonsChunkPlugin       | 将chunks相同的模块代码提取成公共js         |
| CleanWebpackPlugin       | 清理构建目录                               |
| ExtractTextWebpackPlugin | 将css从bundle文件中提取成一个独立的css文件 |
| CopyWebpackPlugin        | 将文件或者文件夹拷贝到构建的输出目录       |
| HTMLWebpackPlugin        | 创建html文件去承载输出的bundle             |
| UglifyjsWebpackPlugin    | 压缩js                                     |



### 8. 核心概念 -- mode

> 用来指定当前的构建环境：production、development、none。
>
> 设置 mode 可以使用 webpack 内置的函数，决定是否开启优化选项，默认值为 production

* development
    * 设置 `process.env.NODE_ENV` 的值为 `development`
    * 开启 `NamedChunksPlugin` 、`NamedModulePlugin`
* production
    * 设置 `process.env.NODE_ENV` 的值为 `production`
    * 开启 ... 等插件
* none
    * 不开启任何优化选项。

### 9. 资源解析

#### 9.1 解析ES6

#### 9.2 解析jsx

#### 9.3 解析css

* `style-loader`
* `css-loader`
* `scss-loader`

#### 9.4 解析图片和字体

`file-loader`



### 10. 文件监听

> 文件监听是在发现源码发生变化时，自动构建出新的输出文件。

* webpack开启监听模式，有两种方式：
    * 启动webpack命令时，带上 `--watch` 参数
    * 配置文件 `webpack.config.js` 中设置参数 `watch: true`

* 缺陷：需要手动刷新浏览器。

* 文件监听的原理：

    * **轮询** 判断文件的最后编辑时间是否发生变化
    * 某个文件发生变化时，不会立即告诉监听者，而是先缓存起来，等 `aggregateTimeout`

    ```JavaScript
    module.exports = {
        // 默认不开启，即为 false
        wathch: true,
        // watch 为true，watchOptions 才有意义
        watchOptions: {
            // 不监听的文件或者文件夹，支持正则，默认为空
            ignored: /node_moduel/,
            // 实施延迟，默认300ms
            aggregateTimeout: 300ms,
            // 判断文件变化时通过不停访问系统文件是否有变化，默认每秒 1000 次
            poll: 1000
        }
    }
    ```

    

 

### 11. 热更新

#### 11.1 热更新方式

* `webpack-dev-server`

    * `--open` 自动打开参数
    * wds不刷新浏览器
    * 不输出文件，而是放在内存中，无IO，构建速度更快。
    * 使用 `HotModuleReplacementPlugin` 插件

* `webpack-dev-middleware`

    * 配合 `express` 或者 `koa`

        ```JavaScript
        const express = require('express');
        const webpakc = require('webpack');
        const webpackDevMiddleware = require('webpack-dev-middleware');
        
        const app = express();
        const config = require('./webpack.config.js');
        const compiler = webpack(config);
        
        app.use(webpackDevMiddleware(compiler, {
            publickPath: config.output.publickPath
        }));
        
        app.listen(3000, () => {
            ...
        });
        ```

    * wdm 将webpack的输出文件传输给服务器，使用与灵活的定制场景

    

#### 11.2 原理分析

    ![](../images//image-20200920235002543.png)


​    

热更新分为两个阶段：启动阶段 和 开发阶段

* 启动阶段：初始代码通过webpack compiler进行打包，推送到同在服务端的 Bundle Server，，让浏览器可以访问得到。
* 开发阶段：文件发生改变时，代码仍会经过 webpack compiler进行编译打包，但此时是推送到同在服务端的HMR Server，让其知晓哪些内容发生变化，HMR Server再通知被注入到浏览器中的HMR Runtime（通常是通过 websocket通信），使用 json格式传输数据，最后是由HMR Runtime 更新浏览器端数据。



### 12. 文件指纹

* 种类
    * Hash
        * 和整个项目有关，只要项目文件有修改，**整个项目**构建的hash都会改变。
    * ChunkHash
        * 和 webpack 打包的 chunk（打包好的模块） 有关，不同的 entry 会生成不同的chunkhash。
    * ContentHash
        * 根据文件内容来定义hash，文件内容不变，则contentHash不变。



### 13. html/css/js压缩

* js 压缩

    * 内置了 uglifyjs-webpack-plugin

* css 压缩

    * `optimize-css-asset-webpack-plugin`

    * 同时使用css处理器 `cssnano`

        ```js
        ...
        plugins: [
            new OptimizeCssAssetsPlugin({
                assetNameRegExp: /\.css$/g,
                cssProcessor: require('cssnano')
            })
        ]
        ...
        
        ```

* html 压缩

    * 修改 `html-webpack-plugin` 参数，设置压缩参数 `minify` 。一个页面一次 `new HtmlWebpackPlugin()`

        ```JavaScript
        ...
        plugins: [
            new HtmlWebpackPlugin({
                template: path.join(__dirname, 'src/index.html'),
                filename: 'index.html',
                chunks: ['index', ]
                inject: true,
                minify: {
                	html5: true,
                	collapseWhitespace: true,
                	preserveLineBreaks: false,
                	minifyCSS: true,
                	minifyJS: true,
                	removeComments: false
            	}
            })
        ]
        ```

        

### 14. 自动清理构建目录

* 避免每次构建前手动清理dist 
    * `clean-webpack-plugin`
    * 默认会删除output指定的输出目录



### 15. 增强css

#### 15.1 PostCss loader插件 autoprefixer 自动补全 css3前缀

* 使用 autoprefixer 插件

    * 根据 Can I Use 规则

    * ```JavaScript
        // webpack.config.js
        ....
        module: {
            rules: [
                {
                    test: /\.scss$/,
                    use: [
                        'style-loader',
                        'css-loader',
                        'scss-loader',
                        'postcsss-loader'
                    ]
                }
            ]
        }
        
        // postcss.config.js
        module.exports = {
            plugins: [
                require('autoprefixer')
            ]
        }
        
        // package.json
        ...
        "browserslist": ["last 2 version", ">1%", "ios 7"],
        ...
        ```

#### 15.2.  移动端css 转换 px 为 rem

* 此前：
    * `@media screen and (max-width: 980) { ... }`
    * 需要写多套适配样式代码
    
* `px2rem-loader` + `lib-flexible`

    ```javascript
    ...
    rules: [
        ...,
        {
        	loader: 'px2rem-loader',
        	options: {
        		remUnit: 75,
        		remPrecision: 8
        	}
        }
    ]
    ```



### 16. 资源内联

#### 16.1 资源内联的意义

* 代码层面：
    * 代码框架的初始化脚本
    * 上报相关的埋点
    * css  内联避免页面闪动
* 请求层面：减少http请求
    * 小图片 或者 字体内联 => `url-loader` 设置 limit

#### 16.2 css 内联

* `style-loader`

    ```javascript
    ...
    {
        test: /\.css$/,
    	use: [
            {
                loader: 'style-loader',
                options: {
                    insertAt: 'top',
                    singleton: true // 将所有的style标签合并成一个
                }
            },
            'css-loader'
        ]
    }
    ```

    

* `html-inline-css-webpack-plugin`

* 

#### 16.3 html/js

`raw-laoder`



### 17. 多页面

> 每一次页面跳转的时候，后台服务器都返回一个新的html文档，这种类型的网站就是多页面网站，也叫多页面应用。多SEO更友好。

* **多页面打包的基本思路：**每个页面对应一个 `entry` 和 一个 `html-webpack-plugin`。缺点是每次新增后者删除页面都需要修改 webpack 配置。

* **多页面打包通用方案**：动态获取 entry 和设置 html-webpack-plugin 

    * 利用 `glob.sync`

        ```javascript
        glob.sync(path.join(__dirname, './src/*/index.js'))
        ```

        

    