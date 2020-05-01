# **webpack**

## 基本配置

下面是一个简单的webpack配置

```javascript
// webpack.config.js

const path = require('path');
module.exports = {
    mode:'development', // 开发模式
    entry: path.resolve(__dirname,'../src/main.js'),    // 入口文件
    output: {
        filename: 'output.js',      // 打包后的文件名称
        path: path.resolve(__dirname,'../dist')  // 打包后的目录
    }
}
```

执行build命令后。会在产生dist/main.js

### 配置html模板

webpack打包出来的js文件我们需要引入到html中，这时候需要一个插件——html-webpack-plugin

```javascript
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development', // 开发模式
    entry: path.resolve(__dirname,'../src/main.js'),    // 入口文件
    output: {
      filename: '[name].[hash:8].js',      // 打包后的文件名称
      path: path.resolve(__dirname,'../dist')  // 打包后的目录
    },
    plugins:[
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/index.html')
      })
    ]
}
```

这会在dist目录下生成一个index.html文件，里面引入了main.js

### 那么多入口文件怎么配置

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development', // 开发模式
    entry: {
      main:path.resolve(__dirname,'../src/main.js'),
      header:path.resolve(__dirname,'../src/header.js')
  }, 
    output: {
      filename: '[name].[hash:8].js',      // 打包后的文件名称
      path: path.resolve(__dirname,'../dist')  // 打包后的目录
    },
    plugins:[
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/index.html'),
        filename:'index.html',
        chunks:['main'] // 与入口文件对应的模块名
      }),
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/header.html'),
        filename:'header.html',
        chunks:['header'] // 与入口文件对应的模块名
      }),
    ]
}
```

### 每次使用build会有上次打包的残留

使用插件clean-webpack-plugin来清空文件夹

```
plugins:[new CleanWebpackPlugin()]
```

### 引用css

需要使用loader来解析css

css-loader     less-loader

```javascript
module.exports = {
    // ...省略其他配置
    module:{
      rules:[
        {
          test:/\.css$/,
          use:['style-loader','css-loader'] // 从右向左解析原则
        },
        {
          test:/\.less$/,
          use:['style-loader','css-loader','less-loader'] // 从右向左解析原则
        }
      ]
    }
} 
```

当css有浏览器前缀使用

npm i -D postcss-loader autoprefixer  

```javascript
module.exports = {
    //...省略其他配置
    module:{
        rules:[{
            test:/\.less$/,
            use:['style-loader','css-loader',{
                loader:'postcss-loader',
                options:{
                    plugins:[require('autoprefixer')]
                }
            },'less-loader'] // 从右向左解析原则
        }]
    }
}
```

### 拆分css

1. 拆分为一个css

   官方推荐使用`mini-css-extract-plugin`插件来打包css文件

   ```javascript
   const MiniCssExtractPlugin = require("mini-css-extract-plugin");
   module.exports = {
     //...省略其他配置
     module: {
       rules: [
         {
           test: /\.less$/,
           use: [
              MiniCssExtractPlugin.loader,
             'css-loader',
             'less-loader'
           ],
         }
       ]
     },
     plugins: [
       new MiniCssExtractPlugin({
           filename: "[name].[hash].css",
           chunkFilename: "[id].css",
       })
     ]
   }
   ```

   

2. 拆分为一一对应的多个css

   使用到`extract-text-webpack-plugin`

   ```javascript
   const path = require('path');
   const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin')
   let indexLess = new ExtractTextWebpackPlugin('index.less');
   let indexCss = new ExtractTextWebpackPlugin('index.css');
   module.exports = {
       module:{
         rules:[
           {
             test:/\.css$/,
             use: indexCss.extract({
               use: ['css-loader']
             })
           },
           {
             test:/\.less$/,
             use: indexLess.extract({
               use: ['css-loader','less-loader']
             })
           }
         ]
       },
       plugins:[
         indexLess,
         indexCss
       ]
   }
   ```

   

### 打包一些图片、字体、媒体等文件

`file-loader`就是将文件在进行一些处理后（主要是处理文件名和路径、解析文件url），并将文件移动到输出的目录中
 `url-loader` 一般与`file-loader`搭配使用，功能与 file-loader 类似，如果文件小于限制的大小。则会返回 base64 编码，否则使用 file-loader 将文件移动到输出的目录中

```javascript
module.exports = {
  // 省略其它配置 ...
  module: {
    rules: [
      // ...
      {
        test: /\.(jpe?g|png|gif)$/i, //图片文件
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                    name: 'img/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/, //媒体文件
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                  name: 'media/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/i, // 字体
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                  name: 'fonts/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
    ]
  }
}
```

### 使用babel转义js

使用babel-loader

```javascript
module.exports = {
    // 省略其它配置 ...
    module:{
        rules:[
          {
            test:/\.js$/,
            use:{
              loader:'babel-loader',
              options:{
                presets:['@babel/preset-env']
              }
            },
            exclude:/node_modules/
          },
       ]
    }
}
```

上面的`babel-loader`只会将 ES6/7/8语法转换为ES5语法，但是对新api并不会转换 例如(promise、Generator、Set、Maps、Proxy等)
此时我们需要借助babel-polyfill来帮助我们转换

```javascript
const path = require('path')
module.exports = {
    entry: ["@babel/polyfill",path.resolve(__dirname,'../src/index.js')],    // 入口文件
}
```







