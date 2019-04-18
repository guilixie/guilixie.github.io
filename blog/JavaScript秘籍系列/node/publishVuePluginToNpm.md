# 发布自己的vue插件到npm

为了方便，采用vue-cli搭建项目，并编写好自己的vue组件。然后修改webpack配置，打包测试插件，书写文档，最后登录npm账号，并发布插件。本文记录了主要流程，作为本次实践的一次记录，完整项目的地址：[vue-resizable-box](https://github.com/guilixie/vue-resizable-box)。

## 编写插件

* 编写好组件后，需要编写入口文件 `index.js`,具体如下:

```js
// index.js
import ResizableBox from './src/ResizableBox'

const Component = {}

Component.installed = false

Component.install = function(Vue, option) {
    // 防止插件多次安装
    if (Component.installed) return

    // 注册组件，这里还注册了一个别名
    Vue.component(ResizableBox.name, ResizableBox)
    Vue.component(`Vue${ResizableBox.name}`, ResizableBox)

    Component.installed = true
}

// 当使用CDN方式引入时，自动调用 `Vue.use`
if (typeof window !== 'undefined' && window.Vue) {
    window.Vue.use(Component)
}

export default Component
```

* 开宗明义，直接上webpack配置文件 `webpack.config.js`,细节请自行查看注释，具体如下:

```js
var path = require('path')
var webpack = require('webpack')
var VueLoaderPlugin = require('vue-loader/lib/plugin')

var isProduction = process.env.NODE_ENV === 'production'
// 切换开发模式和生产模式的入口文件
var entry = isProduction ? './index.js' : './src/dev/main.js'

// 详细配置参考 https://www.webpackjs.com/configuration/output/#output-library
var output = isProduction ? {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: 'vue-resizable-box.js',
    library: 'vue-resizable-box',// library指定的就是你使用require时的模块名
    libraryExport: "default",// 入口点的默认导出将分配给 library
    libraryTarget: 'umd', // 将你的 library 暴露为所有的模块定义下都可运行的方式。它将在 CommonJS, AMD 环境下运行，或将模块导出到 global 下的变量。
    umdNamedDefine: true  // 会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define。
} : {
    path: path.resolve(__dirname, './static'),
    publicPath: '/static/',
    filename: 'build.js'
}

module.exports = {
    entry: entry,
    output: output,
    module: {
        rules: [{
            test: /\.css$/,
            use: [
                'vue-style-loader',
                'css-loader'
            ],
        }, {
            test: /\.vue$/,
            loader: 'vue-loader',
            options: {
                loaders: {}
                // other vue-loader options go here
            }
        }, {
            test: /\.js$/,
            loader: 'babel-loader',
            exclude: /node_modules/
        }, {
            test: /\.(eot|woff|woff2|ttf)([\?]?.*)$/,
            loader: "file-loader"
        }, {
            test: /\.(png|jpg|gif|svg)$/,
            loader: 'file-loader',
            options: {
                name: '[name].[ext]?[hash]'
            }
        }, {
            test: /\.styl(us)?$/,
            use: [
                'vue-style-loader',
                'css-loader',
                'stylus-loader'
            ],
        }]
    },
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.esm.js',
            '@': path.resolve(__dirname, './src')
        },
        extensions: ['*', '.js', '.vue', '.json']
    },
    devServer: {
        host: process.env.HOST || '0.0.0.0',
        port: process.env.PORT && Number(process.env.PORT) || 3000,
        contentBase: path.join(__dirname, 'src/dev'),
        historyApiFallback: true,
        noInfo: true,
        overlay: true
    },
    performance: {
        hints: false
    },
    devtool: '#eval-source-map',
    plugins: [
        new VueLoaderPlugin()
    ]
}

if (isProduction) {
    module.exports.devtool = '#source-map'
        // http://vue-loader.vuejs.org/en/workflow/production.html
    module.exports.plugins = (module.exports.plugins || []).concat([
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: '"production"'
            }
        }),
        new webpack.optimize.UglifyJsPlugin({
            sourceMap: false, // 打包时不用sourceMap文件
            compress: {
                warnings: false
            }
        }),
        new webpack.LoaderOptionsPlugin({
            minimize: true
        })
    ])
}
```
### ⚠️ 注意：
  1. library不能有大写字母/空格/下滑线
  2. library不能和现有包名重复
  3. 配置.npmignore可忽略某些私密文件

## 打包测试

* 执行 `npm run build`, 在dist文件夹下生成打包后的代码
* 测试打包后代码的可执行性，分别用多种方式AMD,ES6,Commonjs以及页面直接引入进行测试
* 有时间最好进行Vue单元测试，可参考 [Vue单元测试实战教程(Mocha/Karma + Vue-Test-Utils + Chai)(https://www.jianshu.com/p/38a37d5fccb2)]

## 书写文档

一般来说,文档的重要性是最容易被忽略的。当你的插件写好后，你自己可能觉得很好用，顿时觉得很有成就感，很优秀了呢。然而，没准只有你自己觉得好用，当其他人来看你的代码，若没有良好的注释和代码习惯，一般是很让人头疼的。

这个时候，一个说明文档就显得十分重要了。你要知道再简单的文档，也胜过毫无文档。

## 发布

1. 首先，你得登录 https://www.npmjs.com/signup，然后注册一个账号
2. 第一次发布，在终端输入`npm adduser`，提示输入账号，密码和邮箱，然后将提示创建成功，此时默认你已经登录了，所以不需要再接着`npm login`；不是第一次发布，则直接`npm login`，然后接着输入账号，密码和邮箱
3. 可通过命令`npm whoami`,查看当前登录的用户
4. 关于语义化版本（Semantic versioning),例如： "version": "x.y.z",通过`npm version <update_type>`自动改变版本,`update_type`为`patch`, `minor` 或 `major`其中之一，分别表示补丁，小改，大改
   * 修复bug,小改动，增加z，对应`patch`
   * 增加了新特性，但仍能向后兼容，增加y，对应`minor`
   * 有很大的改动，无法向后兼容,增加x，对应`major`

5. 直接`npm publish`,很快就会提示发布成功，并会收到邮件通知

6. 如果撤销发布的包，这是一种不好的行为，只允许撤销24小时内，可使用命令`npm unpublish <pkg>[@<version>]`。不过你可以采用代替方法`npm deprecate <pkg>[@<version>] <message>`，`message`就是在任何人尝试安装这个包的时候得到的警告。
7. 即使你撤销了已经发布的包，包名和版本号都已不再可用

#### 参考文章

1. [手把手教你写vue插件并发布（一）](https://www.cnblogs.com/adouwt/p/9211003.html)
2. [利用npm安装/删除/发布/更新/撤销发布包](https://www.cnblogs.com/penghuwan/p/6973702.html)