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

    



