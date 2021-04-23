
# 创建共享eslint config

> 两种方式：全配置 和 预设置。

## 全配置

### 初始化项目
按照约定，命名为 `esling-config-myconfig` 或者 `@scope/eslint-config-myconfig`

### 写入规则

创建`index.js`文件，然后写入：
```javascript
    module.exports = {
        rules: {
            semi: [2, "always"]
        }
    };
```

### 本地测试
1. 先在项目目录下执行 `npm link`，该命令会在全局`node_modules`目录为它创建一个软连接，名字同 `package.json` 中的 `name` 字段。

2. 在测试项目中引入： `npm link packageName`

3. 创建 `.eslintrc`文件，并：
    ```js
    {
        "extends": ["eslint-config-myconfig"]
    }
    ```
### 发布
`npm publish`


## 预配置

预配置利用了 `eslint --init` 的能力，在对话过程中选择定制化的选项，如此快速产生一个配置模板。
而后可以在 `rules` 字段中写入需要覆盖的规则。

其余流程相同。