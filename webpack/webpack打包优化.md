# webpack打包优化

<a name="xMKtR"></a>
## dll优化
⚠️注意：webpack 4的性能已经足够好了，dll优化可以废弃。<br />**背景：**

1. webpack打包都会处理所有的模块包括**第三方依赖**
1. 通常情况下我们不会去修改**第三方模块**

**优化：**提前打包第三方库，项目编译构建的过程中不再打包<br />**代码：**
```javascript
// webpack.dll.js
const path = require('path');
const webpack = require('webpack');

module.exports = {
	entry: {
  	vendor: ['vue/dist/vue.esm.js', 'vue-router', 'axios', 'lodash']
  },
  output: {
  	path: path.join(__dirname, '../static/js'),
    filename: '[name].dll.js',
    library: '[name]_library'
  },
  plugins: [
  	new webpack.DllPlugin({
    	path: path.join(__dirname, '.', '[name]-manifest.json'),
      name: '[name]_library'
    })
  ]
};

```
将dll打包的json给到webpack.config.js
```javascript
// webpack.config.js
// ...
module.exports = {
	//...
  plugins: new webpack.DllReferencePlugin({
  	context: path.join(__dirname, '.'),
    manifest: require('vendor-manifest.json')
  })
  //...
}
```
webpack内置dll plugin需要编写webpack.dll.js和webapck.config.js文件，配置繁琐复杂，可以直接使用第三方[autodll-webpack-plugin](https://www.npmjs.com/package/autodll-webpack-plugin)来快速配置dll。
<a name="zlgUl"></a>
## hard-source-webpack-plugin
在webpack 4中，打包性能已经足够好，dll优化带来的时间上的减少基本可以忽略。但是另一个插件hard-source-webpack-plugin可以带来更好的性能。

