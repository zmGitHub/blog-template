---
title: 使用 webpack + react + redux + es6 搭建团队组件化前端项目
date: 2016-07-03 16:41:33
categories:
  - 前端框架
tags:
  - react
  - webpack
---
　　因为最近在团队中尝试了 webpack、react、react-router、redux、es6 技术栈，所以总结出了一套 boilerplate，以便下次做项目时可以快速开始，并进行持续优化。对应的项目地址：[turbo-admin](https://github.com/zmGitHub/turbo-admin/tree/develop)和[ant-admin](https://github.com/zmGitHub/antd-admin)

# 项目结构规划
每个模块相关的 css、img、js 文件都放在一起，比较直观，删除模块时也会方便许多。
```txt
bin
|-- webpack.config.js               # webpack配置
|-- config.js                       # 配置文件
|-- index.js                        # 主入口文件
|-- build.js                        # 生产配置
|-- webpack.dll.js                  # 动态链接库,优化编译速度
mock                                # 项目文档
|-- api.js                          # API接口配置
|-- index.js                        # mock 假数据入口(9999)
node_modules
src                                 # 项目源码
|-- components                      # 公用组件
|-- views                           # 页面目录
|   |-- views1
|   |   |-- index.js                # 页面逻辑
|   |   |-- index.scss              # 页面样式
|   |   |-- img                     # 页面图片
|   |   |   |-- xx.png
|   |   |-- package.json            # 当做单独模块处理
|   |-- index.html                  # 入口页
|   |-- main.js                     # 入口JS
|-- components                      # 公用组件
|   |-- loading
|   |   |-- index.js
|   |   |-- index.scss
|   |   |-- __tests__
|   |   |   |-- xx.js
|-- store
|   |-- util.js
|   |-- createStore.js
|   |-- location.js
|   |-- reducers.js
|   |-- menu.js
|   |-- requireAuth.js
|-- style                           #公共SCSS目录
|   |-- index.scss
|-- static                          #公共静态目录
|   |-- xx.png
package.json                        #项目依赖
.babelrc                            #用了编译node端的es6
.eslintrc                           #eslint配置文件
.editorconfig                       #代码空格和缩进等配置
READNE.md

```
# 要完成的功能
* 编译 jsx、es6、scss 等资源
* 自动引入静态资源到相应 html 页面
* 实时编译和刷新浏览器
* 按指定模块化规范自动包装模块
* 自动给 css 添加浏览器内核前缀
* 按需打包合并 js、css
* 压缩 js、css、html
* 图片路径处理
* 代码分割
* 语法检查
* 本地接口模拟服务
# 准备工作
根据前面的项目结构规划创建项目骨架,安装最基本的npm 包
```json
{
  "name": "bvs",
  "version": "1.0.0",
  "description": "bvs 数据管理",
  "main": "index.js",
  "scripts": {
    "clean": "rimraf dist",
    "dev": "better-npm-run dev",
    "prod": "better-npm-run prod",
    "mock": "nodemon --exec babel-node mock/"
  },
  "betterScripts": {
    "dev": {
      "command": "webpack-dashboard -c magenta -- nodemon --exec babel-node bin/ --ignore dist --ignore src",
      "env": {
        "NODE_ENV": "development",
        "DEBUG": "app:*"
      }
    },
    "prod": {
      "command": "npm run clean && babel-node bin/build",
      "env": {
        "NODE_ENV": "production",
        "DEBUG": "app:*"
      }
    }
  }
  .....
  .....
}
```
具体参考: [turbo-admin](https://github.com/zmGitHub/turbo-admin/tree/develop)和[ant-admin](https://github.com/zmGitHub/antd-admin)


# webpack 配置
```javascript
import Webpack from 'webpack';
import cssnano from 'cssnano';
import HappyPack from 'happypack';
import HtmlWebpackPlugin from 'html-webpack-plugin';
import ExtractTextPlugin from 'extract-text-webpack-plugin';
import OpenBrowserWebpackPlugin from 'open-browser-webpack-plugin';

import _debug from 'debug';

import config from './config';

const debug = _debug('app:webpack.config');
const paths = config.utils_paths;
const BASE_CSS_LOADER = 'css?sourceMap&-minimize';
// 获取当前的执行环境
const {
  __DEV__,
} = config.globals;
debug('👻 初始化 webpack 默认配置...');
const APP_ENTRY = ['babel-polyfill', 'eventsource-polyfill', paths.src('main.js')];
const webpackConfig = {
  name: 'client',
  target: 'web', // 打包成什么类型
  devtool: config.devtool,
  entry: {
    app: __DEV__ ? APP_ENTRY.concat(`webpack-hot-middleware/client?path=${config.public_path}__webpack_hmr`) : APP_ENTRY,
    vendor: config.vendor, // 第三方包
  },
  output: { // 输出配置
    filename: __DEV__ ? '[name].js?[hash]' : '[name].[chunkhash].js',
    chunkFilename: __DEV__ ? '[name].[id].js?[hash]' : '[name].[id].[chunkhash].js',
    path: paths.dist(),
    publicPath: config.public_path,
  },
  resolve: {
    root: paths.src(), // 根目录: 这样可以在内部使用时 是从 src 目录查找 不需要 ../../../ 这样的写法
    modulesDirectories: ['node_modules', paths.base('node_modules')],
    extensions: ['', '.js', '.jsx', '.json'],
    // 导入时可以不用加后缀名, 但是一定要加个空字符因为覆盖了默认的导入行为, 比如导入 a.txt 文件就报错了
    alias: {
      styles: paths.src('styles'),
    },
  },
  .....
```
上面配置好以后执行:
```bash
npm run dev
```
之后你就可以看到:
{% asset_img dashboard.png %}
项目中编译 jsx 的时候没有启用.babelrc 文件这个文件只有启动node端的时候才会用到:
```json
{
  "presets": [
    "es2015",
    "stage-0"
  ],
  "plugins": ["add-module-exports", "transform-runtime"],
  "sourceMaps": true, // 方便测试
  "retainLines": true
}

```
服务端代码和热部署功能
```javascript
import Koa from 'koa';
....

const debug = _debug('app:server');
const paths = config.utils_paths; // 文件目录 utility
const port = config.server_port; // 服务器端口
const host = config.server_host; // 服务 ip
const app = new Koa(); // 创建 koa 的实例

// 是否启用 API 服务器
if (config.proxy && config.proxy.enabled) {
  app.use(convert(proxy(config.proxy.options)));
}

// 让 url 永远指向 index.html 方便 react-outer
app.use(convert(historyApiFallback({
  verbose: false, // 取消日志消息
})));

// 区分生产和开发
if (config.env === 'development') {
  debug('👽 开发模式加载完成');
  const {
    publicPath,
  } = webpackConfig.output;
  const complie = webpack(webpackConfig);
  complie.apply(new DashboardPlugin());
  app.use(convert(webpackDevMiddleware(complie, {
    publicPath,
    contentBase: paths.src(),
    // 服务一起动开始 webpack 编译
    lazy: false,
    hot: true,
    quiet: true, // 是否在 console 里面显示信息
    noInfo: true, // 是否在命令行中显示错误
    stats: {
      colors: true,
    },
    historyApiFallback: true,
  })));
  // 启用热部署插件
  app.use(convert(webpackHotMiddleware(complie)));
  // 静态文件请求
  app.use(convert(server(paths.src('static'))));
} else {
  debug('😻 打包完成, 模拟线上环境');
  app.use(convert(server(paths.dist())));
}

```
还需在代码里添加如下代码监听模块更新
```javascript
// 开发环境配置: 生产下应该去掉
if (__DEV__ && module.hot) {
  const renderApp = render;
  // 调用 readbox 显示开发中的错误
  const renderError = (error) => {
    const RedBox = require('redbox-react').default;
    ReactDOM.render(<RedBox error={error} />, MOUNT_NODE);
  };

  render = () => {
    try {
      renderApp();
    } catch (error) {
      renderError(error);
    }
  };

  // 建立热部署
  module.hot.accept('./views/index', () => {
    setImmediate(() => {
      // 先卸载之前的应用
      ReactDOM.unmountComponentAtNode(MOUNT_NODE);
      render();
    });
  });
}

// 页面渲染
render();
```
# scss图片等静态文件处理

开发模式下基本都是在style 标签中所以项目启动后会白屏一下, 后面打包的时候这些问题将会解决掉:
```javascript
 // sassloader 配置
  sassLoader: {
    includePaths: paths.src('styles'),
  },
  // postcss 配置
  postcss: [
    cssnano({
      autoprefixer: {
        add: true,
        remove: true,
        browsers: ['last 2 versions', 'Firefox ESR', '> 1%', 'ie >= 9', 'iOS >= 8', 'Android >= 4'],
      },
      discardComments: {
        removeAll: true,
      },
      discardUnused: false,
      mergeIdents: false,
      reduceIdents: false,
      safe: true,
      sourcemap: true,
    }),
  ],
```
使用ExtractTextPlugin插件单独打包 css 文件:
```javascript
// 打包配置 将 css 从 style 标签里面抽到 app.css 里面
// 因为开发环境下没有启用 ExtractTextPlugin 所以样式都是在 style 标签里面
if (!__DEV__) {
  debug('启用 ExtractTextPlugin...');
  webpackConfig.module.loaders.filter(loader =>
    loader.loaders && loader.loaders.find(name => /css/.test(name.split('?')[0]))
  ).forEach((loader) => {
    const cssLoader = loader;
    const first = cssLoader.loaders[0];
    const rest = cssLoader.loaders.slice(1);
    cssLoader.loader = ExtractTextPlugin.extract(first, rest.join('!'));
    delete cssLoader.loaders;
  });

  webpackConfig.plugins.push(
    new ExtractTextPlugin('[name].[contenthash].css', {
      allChunks: true,
    }));
}
```
#本地接口模拟服务
前后端分离后, 前端的工作不受后台的影响, 所以我们的工作是并行的, 前端的接口数据格式由我们定, 我这里的处理方式是, koa 里面开启 proxy, 只有发现是 /api/** 打头的就代理的到 mock 端:
```javascript
import Koa from 'koa';
import Mock from 'mockjs';
import pathToRegexp from 'path-to-regexp';
import _debug from 'debug';
import APIS from './api';

const info = _debug('app:mock');

const app = new Koa();

app.use(async(ctx, next) => {
  const reqAPI = ctx.path;
  let mockConfig = null;
  APIS.forEach((api) => {
    const regexp = api.URL.includes(':') && pathToRegexp(api.URL);
    if (regexp && regexp.test(reqAPI)) {
      mockConfig = api[ctx.method];
      return;
    } else if (api.URL === reqAPI) {
      mockConfig = api[ctx.method];
      return;
    }
  });
  const responseData = Mock.mock(mockConfig || {
    success: false,
    error: `没有配置${reqAPI}, 请添加`,
    data: null,
  });
  ctx.body = responseData;
  console.log(`接口地址: http://localhost:9999${reqAPI}`);
  await next();
});

app.listen(9999, (err) => {
  if (err) {
    info(err);
  }
  info('📢 mockser 启动成功, 端口: 9999');
});

```
　　这里的核心模块是path-to-regexp和 mockJS 所以如果想配置接口其实也是非常的容易只需要在 api.js这个文件里面添加 API 路径和返回的数据格式就可以了:
```javascript
const API = [{
  URL: '/api/user/:id(\\d+)', // 请注意如果要指明 id 为数字请添加(\\d+)
  GET: {
    'success|1': [true, false],
    result: {
      total: 10,
      'data|100': [{
        username: '@CNAME',
        'userId|+1': 1,
        clientName: '@CTITLE(2, 5)',
        'pointScore|1-99': 0,
        'freezePointScore|1-99': 0,
        registerDate: '@DATE(yyyy-MM-dd)',
        lastLogin: '@TIME(a HH:mm:ss)',
        'locked|1': [true, false],
      }],
    },
    error: '',
  },
  ....
```
最终打包发布不受任何的影响, 是不是非常的方便呢
# webpack问题和优化
可能眼尖的小伙伴发现了我们项目的编译时间有点长(启动的时候), 打包时也是非常的长20s 左右, 我尝试过的方法是:
* 将第三方的包全局引入
* 使用 happypack
* 使用 dll 方案
* 使用 antd 的时候添加babel-plugin-import, 切记不要再引入antd.less了 这样会重复引入
> 这里说个题外话, ie8和 ie9对于 单个 css 文件的大小不能超过288k, 小伙伴们注意了! 要不你会发现你的样式一半有一半没有, 然后你就会开始怀疑各种人生, 吼吼.....

happypack 基本是解决了编译单线程的问题,所以在配置的时候注意下参数:
```javascript
new HappyPack({
  id: 'babel',
  theads: 4, // 官方推荐,测试发现这个数值是最快的
  loaders: [{
    path: paths.nodeModules('babel-loader/lib/index.js'),
    query: `?${JSON.stringify(config.babelConfig)}`,
  }],
}),
```
对于 dll 的方案我不是很推荐, 无非就是生成一些映射,但是感觉没有什么用啊, 速度什么的没有提上来:
```javascript
// 第三方依赖库
const VENDORS = [
  'antd',
  'echarts',
  'superagent',
  'babel-polyfill',
  'eventsource-polyfill',
  'element-resize-event',
  'react',
  'react-dom',
  'react-redux',
  'react-router',
  'redux',
  'redux-thunk',
];

module.exports = {
  context: base(),
  entry: {
    vendor: VENDORS,
  },
  output: {
    path: base('dll'),
    filename: '[name].dll.js',
    library: '[name]',
  },
  plugins: [
    new webpack.DllPlugin({
      path: base('manifest.json'),
      name: '[name]',
      context: base(),
    }),
  ],
};
```

#结语

　　开发这套体系大概花费了我一个多星期, 中间不少的修修补补, 很多问题都是在项目实际开发中发现的, 然后遇到问题解决, 记录下来, 方便下次看, 这个体系我会继续维护下去,[turbo-admin](https://github.com/zmGitHub/turbo-admin/tree/develop)　这个是我自己写的一些样式组件,[ant-admin](https://github.com/zmGitHub/antd-admin) 这个用的是 antd 的,目前项目基本在跑这个, 欢迎大家 star!
