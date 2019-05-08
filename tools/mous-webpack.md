## Mous UI webpack 搭建
#### 1. 创建目录和远程仓库
[git与Github的使用](https://github.com/clayJa/blog/blob/master/tools/git.md)
#### 2. npm初始化
> npm init -y
#### 3. 安装webpack
> yarn add webpack webpack-cli --dev
#### 4. 新建lib/index.tsx
> yarn add typescript --dev
#### 5. 新建 webpack.config.js
```javascript
module.exports = {}
```
+ 配置 entry
```javascript
module.exports = {
    entry: {
        // MOUSUI 为输出的文件名
        MOUSUI: './lib/index.tsx'
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    }
}
```
这里使用的是*awesome-typescript-loader*这个loader
> yarn add awesome-typescript-loader --dev
+ 配置 output
```javascript
const path = require('path')
module.exports = {
    entry: {
        // MOUSUI 为输出的文件名
        MOUSUI: './lib/index.tsx'
    },
    output: {
        path: path.resolve(__dirname, 'dist/lib'),
        library: 'Mous-UI',
        //  配置库格式-umd
        libraryTarget: 'umd',
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    }
}
```
试试运行webpack
> npx webpack

+ 配置 tsconfig.json , tslint.json —— 直接CV大法 [tsconfig.json](https://github.com/clayJa/mous-react/blob/master/tsconfig.json) [tslint.json](https://github.com/clayJa/mous-react/blob/master/tslint.json)


+ 添加 webpack 的 resolve 配置
+ 配置 module.rules
  - a. jsx
  - b. tsx
  - c. scss
+ 配置 plugins
```JavaScript
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: {
        // MOUSUI 为输出的文件名
        MOUSUI: './lib/index.tsx'
    },
    resolve: {
        extensions: ['.ts', '.tsx', '.js', '.jsx'],
    },
    output: {
        path: path.resolve(__dirname, 'dist/lib'),
        library: 'Mous-UI',
        //  配置库格式-umd
        libraryTarget: 'umd',
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            title: 'Mous-UI',
            template: 'index.html'
        })
    ]
}

```
#### 6. 配置webpack-dev-server 与 webpack.config.dev.js
> yarn add webpack-dev-server --dev
> yarn add html-webpack-plugin --dev
> npx webpack-dev-server
#### 7. 创建 index.html
#### 8. 配置 webpack.config.prod.js
区分开发环境与生产环境（[webpack.config.js](https://github.com/clayJa/mous-react/blob/master/webpack.config.js)、[webpack.config.dev.js](https://github.com/clayJa/mous-react/blob/master/webpack.config.dev.js)、[webpack.config.prod.js](https://github.com/clayJa/mous-react/blob/master/webpack.config.prod.js)）

package.json 的 scripts 中添加 start 和  build 命令, 安装跨环境插件cross-env
  ```json
  "scripts": {
    "start": "cross-env NODE_ENV=development webpack-dev-server --config webpack.config.dev.js",
    "build": "cross-env NODE_ENV=production webpack --config webpack.config.prod.js"
  },
  ```
  > yarn add cross-env --dev
#### 9. 创建 example 预览与 webpack.config.docs.js
#### 10. 引入测试
> yarn add --dev jest babel-jest @babel/preset-env @babel/preset-react react-test-renderer

添加.babelrc
```json
{
    "presets": [
        "react-app"
    ]
}
```

配置[jest.config.js](https://github.com/clayJa/mous-react/blob/master/jest.config.js)[tsconfig.test.json](https://github.com/clayJa/mous-react/blob/master/tsconfig.test.json),安装ts-jest
> yarn add ts-jest --dev
新建 test/setupTests.js

package.json 的 scripts 中添加 test 命令
```json
  "scripts": {
    "test": "cross-env NODE_ENV=test jest --config=jest.config.js --runInBand",
  },
```
新建 __test__/hello.unit.tsx
> yarn add @types/jest --dev

#### 11. 引入CI
#### 12. 自定义任务
#### 13. 根据需要引入其他loader
+ 引入 svg loader
  - 配置rule
    ```JavaScript
        rules: [
            ...
            {
                test: /\.svg$/,
                loader: 'svg-sprite-loader',
            },
            ...
        ]
    ```
    > yarn add --dev svg-sprite-loader
    - 声明types lib/types/custom.d.tsx
    ```TypeScript
    declare module '*.svg' {
        const content: any;
        export default content;
    }
    ```
    - 配置 tsconfig.json
    ```json
    "include": [
        "lib/**/*"
    ],
    ```
+ 引入scss loader
    - webpack.config.js
    ```JavaScript
    {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
    },
    ```
    >  yarn add --dev style-loader css-loader sass-loader
    >  yarn add --dev node-sass 
