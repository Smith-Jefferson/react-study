# webpack与react
## webpack配置

#### 0.初始化项目
     cd 'your/path/' npm init;  
#### 1.安装webpack
     全局安装：cnpm install webpack -g;
     项目内安装：cnpm install webpack --only=dev --save
#### 2.配置webpack.config.js文件
```
     const path = require('path');

     module.exports = {
         entry: './Script/main.js', //项目入口文件
         output:{                    //输出编译后文件地址及文件名
             path: path.resolve(__dirname, 'dist'),
             filename: 'bundle.js'
         }
     };
```          
执行 webpack 命令即可看到编译后的文件
#### 3安装webpack-html-plugin
$ npm install html-webpack-plugin --save-dev
```
    plugins:[
        ...
        new HtmlWebpackPlugin({
            title:'react 学习',
            inject:'body',
            filename:'index.html',
            template:path.resolve(__dirname, "index.html")
        }),
        ...
    ]
```          
自动根据模板生成HTML文件并自动引入打包生成的js文件

## 安装react
#### 4.安装 react react-dom babel

npm install react react-dom --save

配置laoder
```
...
module:{
        loaders:[
            {
                test: /\.jsx$/,
                exclude: /^node_modules$/,
                use: [{loader:'babel-loader'}],
                include: [APP_PATH]
            }
        ]
    }
    ...
```
```
    npm install babel-preset-react --save-dev
    npm install babel-loader babel-core babel-preset-es2015 --save-dev
    npm install babel-preset-stage-0 babel-preset-stage-1 babel-preset-stage-3 --save-dev
```

配置 .babelrc

```
   {
    "presets": [
        "react",
        "es2015",
        "stage-0",
    ]
    ...
}
```
更改main.js为main.jsx修改代码为
```
    import React from 'react';
    import ReactDOM from 'react-dom';

    class MainView extends React.Component{
        constructor(props){
            super(props)
        }
        render(){
            return (<div>
                测试
            </div>)
        }
    }
    ReactDOM.render(
        <MainView/>,
        document.getElementById('main')

    )
```
打开打包生成的页面即可看到
![index1](https://github.com/liubin915249126/react-study/blob/master/images/index1.png)

#### 5.使用webpack-dev-server
npm install webpack-dev-server --save-dev
配置webpack.config.js
```
    ...
    devServer: {
        historyApiFallback: true,
    },
    ...
```  
配置package.json里面命令
```
    "start":"webpack-dev-server --hot --inline --progress --open"
```
执行 npm start 浏览器自动打开页面，更改文件后即可看到页面实时更新
![index2](https://github.com/liubin915249126/react-study/blob/master/images/index2.png)

## 使用路由
#### 6.react-router4.2.0
     npm install --save react-router
     npm install --save react-router-dom
react-router4.0相对于之前版本变化比较大
```
     ...
     import { Route, Link, Switch, BrowserRouter, HashRouter } from 'react-router-dom';
     ...
     render(){
        return(
            <BrowserRouter>
                <Route path="/" component={Main} />
            </BrowserRouter>
        )
    }

```     
参考文献[react4.0初探](http://blog.csdn.net/sinat_17775997/article/details/69218382)
[github](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/migrating.md)地址

## 7.安装 antd
npm install antd --save
按需加载组件 npm install babel-plugin-import --save-dev
配置 .babelrc
```
    ...
   "plugins": [["import", {
    "libraryName": "antd",
    "style": true
  }]]
```
#### 8.配置webpack.config.js加载less/css文件
npm install less-loader css-loader style-loader less --save -dev
(坑3忘了安装less)
参考文献[less-loader](https://github.com/webpack-contrib/less-loader)
```{
    test:/\.less$/,
    use: [{ loader: 'style-loader' }, { loader: 'css-loader' }, { loader: 'less-loader' }]
   }
```
加载less文件加载组件
```
   ...
   import 'antd/dist/antd.less';
   import {DatePicker,message} from 'antd';
   ...
   render() {
        return (<div>
            <DatePicker onChange={value => this.handleChange(value)} />
        </div>)
```
效果图![效果图](https://github.com/liubin915249126/react-study/blob/master/images/antd1.png)
## 使用nodejs+koa2提供后台接口

npm install koa koa-router --save-dev
>
在根目录下下新建server/index.js文件index.js:
>
```
    const Koa = require('koa');
    const router = require('koa-router')();
    const app = new Koa();
    router.get('/', function (ctx, next) {
        ctx.response.body = '111'
    });

    app
        .use(router.routes())
        .use(router.allowedMethods());

    app.listen(3000);
    console.log('server is start')
```
>
浏览器里面访问localhost/3000可看到返回值
>
#### 6.分离公共代码
webpack配置
```
    module.exports = {
        entry: {
            main: './Script/main.js',
            vandor:['jquery']
        },
        output:{
            path: path.resolve(__dirname, 'dist'),
            filename: '[name].[hash].bundle.js'
        },
        plugins:[
            ...
            new webpack.optimize.CommonsChunkPlugin({
                names: ["vandor", "manifest"]
            })
        ]
    }
```
