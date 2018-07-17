### 简单使用
index.html
```
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    <script src="./build/bundle.js"></script>
</body>
</html>
```

简单配置 webpack.config.js
```
// 自带的库
const path = require('path')
module.exports = {
    entry:  './app/index.js', // 入口文件
    output: {
      path: path.resolve(__dirname, 'build'), // 必须使用绝对地址，输出文件夹
      filename: "bundle.js" // 打包后输出文件的文件名
    }
  }
```

因为我们是在文件夹下安装的webpack，每次运行都要输 node_modules/.bin/webpack 过于繁琐，在 package.json 如下修改
```
"scripts": {
    "start": "webpack"
  },
```
然后再次执行 npm run start，和之前效果相同

### Loader
## Babel
Babel 可以让你使用 ES2015/16/17 写代码而不用顾忌浏览器的问题，Babel 可以帮你转换代码。

依赖的库：
```
npm i --save-dev babel-loader babel-core babel-preset-env
```

- babel-loader 用于让 webpack 知道如何运行 babel
- babel-core 可以看做编译器，这个库知道如何解析代码
- babel-preset-env 这个库可以根据环境的不同转换代码

更改 webpack-config.js 中的代码
```
module.exports = {
// ......
    module: {
        rules: [
            {
            // js 文件才使用 babel
                test: /\.js$/,
             // 使用哪个 loader
                use: 'babel-loader',
            // 不包括路径
                exclude: /node_modules/
            }
        ]
    }
}
```

配置 Babel 推荐使用 .babelrc 文件管理
```
// ..babelrc
{
    "presets": ["babel-preset-env"]
}
```

### 处理图片
依赖的库：
```
npm i --save-dev url-loader file-loader
```

创建一个js文件处理图片(eg:addImage.js)
```
// addImage.js
let smallImg = document.createElement('img')
// 必须 require 进来
smallImg.src = require('../images/small.jpeg')
document.body.appendChild(smallImg)

let bigImg = document.createElement('img')
bigImg.src = require('../images/big.jpeg')
document.body.appendChild(bigImg)
```

修改 webpack.config.js 的代码
```
module.exports = {
  // ...
  module: {
    // ...
    output: {
      publicPath: 'build/' // 知道如何寻找资源
    },
    rules: [
      // ...
      {
        // 图片格式正则
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        use: [{
          loader: 'url-loader',
          // 配置 url-loader 的可选项
          options: {
            // 限制 图片大小 10000B，小于限制会将图片转换为 base64格式
            limit: 10000,
            // 超出限制，创建的文件格式
            // build/images/[图片名].[hash].[图片格式]
            name: 'images/[name].[hash].[ext]'
          }
        }]
      }
    ]
  }
}
```
运行 npm run start 后刷新页面

### 处理CSS文件
依赖的库：
```
npm i --save-dev css-loader style-loader
```

- css-loader 可以让 CSS 文件也支持 impost，并且会解析 CSS 文件
- style-loader 可以将解析出来的 CSS 通过标签的形式插入到 HTML 中，依赖 css-loader

修改 addImage.js 文件
```
import '../styles/addImage.css'

let smallImg = document.createElement('img')
smallImg.src = require('../images/small.jpeg')
document.body.appendChild(smallImg)
```

修改 webpack.config.js
```
module.exports = {
  // ...
  module: {
    rules: [{
      test: /\.css$/,
      use: ['style-loader',
        {
          loader: 'css-loader',
          options: {
            modules: true
          }
        }
      ]
    }]
  }
}
```
运行 npm run start 后刷新页面

原文地址：https://juejin.im/post/59bb37fa6fb9a00a554f89d2 还有很多操作没有实践，后面再慢慢补充