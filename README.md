# react01
##一.使用webpack搭建react项目

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
 
 webpack 的配置文件，是导出一个对象的 JavaScript 文件。在根目录创建config文件夹，添加一个js文件，命名为webpack.base.js
 
        const path = require('path');
        const DIST_PATH = path.resolve(__dirname, '../dist');
        module.exports = {
            entry: {
                app: './app/index.js'
            },
            output: {
                filename: "js/bundle.js",
                path: DIST_PATH
            }
        };
      
 webpack4.0增加了mode属性用来表示不同的环境，我们可以使用merge的方式来组织webpack的基础配置和不同环境的配置
 安装webpack-merge：
 
        npm install --save-dev webpack-merge
 
 在config文件夹中再添加一个js文件，命名为webpack.prod.js
 
        const merge = require('webpack-merge');
        const baseWebpackConfig = require('./webpack.base.js');
        module.exports = merge(baseWebpackConfig, {
          mode: 'production'
        });
        
 3.目录结构 
 
        |- /app
          |- index.js
        |- /node_modules
        |- /public
          |- index.html
        |- /config
          |- webpack.base.js
          |- webpack.prod.js
        |- package.json
        |- package-lock.json

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
 
 
 ##二、 react搭建项目优化
 
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
            
  4.src下新建App.js
  
            import React, { Component } from 'react';
            class App extends Component {
                render() {
                    return (
                          <div>NIhao</div>
                    );
                }
            }
            export default App
   
   5.以脚本方式执行构建
         编辑package.json，加入自定义脚本，在项目根目录执行npm run dev即可达到上面一样的效果。
    
            "scripts": {
                "dev": "webpack --config webpack/webpack.config.js"
             }
             
   6.搭建前端服务器：可以发现每次都要重新构建然后刷新index.html才能看到最新的效果，这样开发效率极低，这时就需要webpack-dev-server，
        webpack-dev-server是一个小型的静态文件服务器，为webpack打包的资源文件提供Web服务。并且提供自动刷新和Hot Module Replacement（模块热替         换：前       端代码变动后无需刷新整个页面，只把变化的部分替换掉）    
    搭建webpack-dev-server：
    
            npm install --save-dev webpack-dev-server;
     
   7.在项目根目录下创建bin目录，进入bin目录，创建dev-server.js文件
   
        'use strict'
        const WebpackDevServer = require('webpack-dev-server');
        const config = require('../webpack/webpack.config');
        const webpack = require('webpack');
        const path = require('path');
        const compiler = webpack(config);

        const server = new WebpackDevServer(compiler, {
            contentBase: path.resolve(__dirname, '../dist'), //默认会以根文件夹提供本地服务器，这里指定文件夹
            historyApiFallback: true, //在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
            port: 9090, //如果省略，默认8080
            publicPath: "/"
        });
        server.listen(9090, 'localhost', function (err) {
            if (err) throw err
        })
        
   8.编辑package.json，创建一条脚本
        
            "scripts": {
                "dev": "node bin/dev-server"
             }
             
  9.执行npm run dev 修改项目代码，项目会自动重新编译运行，刷新浏览器即可看到最新的更改
    
