---
title: webpack
date: 2019-05-25 10:48:16
tags:
---

### 相關工具、套件、技術
*webpack、webpack-cli、webpack-dev-server、編譯pug、編譯sass*

### 目的
> 為求開發時，簡化重複的流程、建立一個良好的本機開發環境

### 步驟

1. 安裝node js
   > [https://nodejs.org/en/](https://nodejs.org/en/)

2. 初始化專案
   ``` bash
    npm init -y
   ```

3. 安裝所需套件
   ```bash
    // 安裝 webpack 所需要的模組
    npm install -D webpack webpack-cli webpack-dev-server
    npm install -D html-webpack-plugin
    npm install -D extract-text-webpack-plugin
    npm install -D clean-webpack-plugin

    // 安裝編譯 pug 需要的模組
    npm install -D pug pug-loader

    // 安裝編譯 sass 需要的模組
    npm install -D sass-loader node-sass

    // 安裝打包 css 時，所需要的模組
    npm install -D css-loader style-loader
   ```
4. 新增以下檔案、資料夾
   * webpack.config.dev.js
   * webpack.config.prod.js
   * src
      - index.js
      - main.js
      - main.pug
      - main.scss

5. 寫 webpack 設定檔
   > webpack.config.dev.js
   ``` javascript
    var webpack = require('webpack')
    var HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
      entry: './src/index.js', // 進入點
      output: {
        path: __dirname + '/dist', // 輸出的資料夾位置
        filename: '[name].[hash:8].js' // 輸出的檔名
      },
      plugins: [
        new HtmlWebpackPlugin({ // 用來產生 html頁面
          template: './src/main.pug'
        }),
        new webpack.NamedModulesPlugin(), // hot reload module
        new webpack.HotModuleReplacementPlugin() // hot reload module
      ],
      module: {
        rules: [
          {
            test: /\.pug$/,
            use: ['pug-loader']
          },
          {
            test: /\.scss$/,
            use: ['style-loader', 'css-loader', 'sass-loader']
          }
        ]
      }
    };
   ```
  > webpack.config.prod.js
   ``` javascript
    var HtmlWebpackPlugin = require('html-webpack-plugin')
    var ExtractTextPlugin = require('extract-text-webpack-plugin')
    var CleanWebpackPlugin = require('clean-webpack-plugin')

    module.exports = {
      entry: './src/index.js', // 進入點
      output: {
        path: __dirname + '/dist', // 輸出的資料夾位置
        filename: '[name].[hash:8].js' // 輸出的檔名
      },
      plugins: [
        new CleanWebpackPlugin(), // build的時候先清理 dist資料夾
        new HtmlWebpackPlugin({ // 用來產生 html頁面
          template: './src/main.pug'
        }),
        new ExtractTextPlugin('style.css'), // 將 bundle.js 的 css 拆出來
      ],
      module: {
        rules: [
          {
            test: /\.pug$/,
            use: ['pug-loader']
          },
          {
            test: /\.scss$/,
            use: ExtractTextPlugin.extract({
                fallback: 'style-loader',
                //resolve-url-loader may be chained before sass-loader if necessary
                use: ['css-loader', 'sass-loader']
            })
          }
        ]
      }
    };
  ```

6. 將 main.js、main.pug、main.scss 載進 index.js
   ``` javascript
    require('main.pug')
    require('main.js')
    require('main.scss')
   ```
   > 然後在 main.js、main.pug、main.scss 開發即可

7. 在 package.json 新增指令
   ``` json
    "scripts": {
      "dev": "webpack-dev-server --hot --open --port=8080",
      "build": "webpack"
    }
   ```

8. 在命令列輸入命令
   ``` bash
    // 開發環境
    npm run dev

    // 打包輸出
    npm run build
   ```
