# 简单实现一个ejs-loader

## 一、目的
目前，webpack配置中，对于模板文件的处理大体上有两种：
1. 脚本文件中，导入模板文件。
2. 使用html-webpack-plugin给指定的bundle指定模板文件。

一般来说，为了让打包完成时候，html文件中可以自动引入打包出来的js脚本和样式文件，我们会使用html-webpack-plugin的。

但是，在使用html-webpack-plugin时有几个问题：
1. 这个plugin默认是使用lodash template的。这是一个lodash库提供的模板引擎，并不能支持全部ejs功能。
2. 模板引擎会被在loader中配置的对应loader覆盖掉。比如模板指定为html文件，又在loader中配置了html文件对应的loader为html-loader，那么插件会使用html-loader而不会使用lodash-template。（详见[html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)）
3. 网上居然找不到使用ejs2 engine的ejs-loader。

而我想使用所有的ejs2功能的话，就需要自己将ejs封装成一个loader。

## 二、实现
其实loader的思路很简单，一个loader就是一个模块，导出一个方法函数。这个函数有个输入输出，只要输入输出格式符合要求，就可以和其他loader实现链式调用。

而我的ejs-loader只需要实现一个功能，接收模板文件字符串然后使用ejs engine渲染成html字符串文本。

至于html中的图片链接的处理，可以

