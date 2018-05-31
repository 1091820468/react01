# react01
一.使用webpack搭建react项目

1.创建一个文件夹first-react，进入该目录，在该目录下打开一个终端，执行npm init
    完成后，npm会自动在你的根目录生成 package.json 文件,package.json 文件不仅仅包含项目的配置信息（比如名称、版本、许可证等元数据），还会记录项目所     需要的各种模块，以及项目运行的脚本等等。
    
2.安装react, react-dom, webpack   (dev开发时依赖)
    
    npm install react react-dom --save
    npm install webpack --save-dev
     
     "dependencies": {
         "react": "^16.4.0",
         "react-dom": "^16.4.0"
      },
      "devDependencies": {
         "webpack": "^4.10.2"
      }
      
 3.目录结构 
 
       |--dist(项目打包输出目录)
            |--bundle.js(该文件是由webpack打包生成)
            |--index.html　　
       |--src
            |--index.js
       |--webpack
            |--webpack.config.js
       |--package.json
       
   index.html
    
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
        </head>
        <body>
            <div id="app">
                /* react DOM*/
            </div>
            <script src="bundle.js"></script>
        </body>
        </html>
        
   index.js
    
        import React, { Component } from 'react';
        import { render } from 'react-dom';
        render(
            <div>Hello World</div>,
            document.getElementById('app')
        )
        
   webpcak.config.js
    
        const path = require('path');
        module.exports = {
            entry: path.resolve(__dirname, '../src/index.js'), //指定入口文件，程序从这里开始编译,__dirname当前所在目录, ../表示上一级目                                                                   录, ./同级目录
            output: {
                path: path.resolve(__dirname, '../dist'), // 输出的路径
                filename: 'bundle.js'  // 打包后文件
            },
            module: {
                rules: [
                    {
                        test: /\.(js|jsx)$/,
                        use: {
                            loader: 'babel-loader',
                            options: {
                                presets: ['es2015', 'react'],
                            }
                        },
                        exclude: /node_modules/
                    }
                ]
            }
        }
 
 4.这里如果在项目根目录直接进行webpack构建会报错(构建命令：webpack --config webpack/webpack.config.js)，
    因为我们使用了react，react是使用jsx语法实现的，而jsx不能直接被浏览器识别和执行，所以这里需要引入Babel库进行转码。
    npm install babel-core babel-loader babel-preset-es2015 babel-preset-react --save
            
              babel-loader: babel加载器
              babel-preset-es2015: 支持es2015
              babel-preset-react: jsx 转换成js
        
 
 5.执行webpack： webpack --config webpack/webpack.config.js
 
 
 二、 react搭建项目优化
 
  1.index.js改写
  
        import React from 'react';
        import { render } from 'react-dom';
        import App from './App'
        const renderDom = Component => {
            render(
                <Component />,
                document.getElementById('app')
            );
        }
        renderDom(App);
   
  2. 在项目根目录下新建json文件.babelrc，将babel的配置单独提取出来
  
            {
              "presets": [
                "es2015",
                "react"
              ]
            }
  
  3. webpack.config.js做相应的调整
  
            const path = require('path');
            module.exports = {
                entry: path.resolve(__dirname, '../src/index.js'), //指定入口文件，
                output: {
                    path: path.resolve(__dirname, '../dist'), // 输出的路径
                    filename: 'bundle.js'  // 打包后文件
                },
                module: {
                    rules: [
                        {
                            test: /\.(js|jsx)$/,
                            loader: 'babel-loader',
                            exclude: /node_modules/
                        }
                    ]
                }
            }
    
