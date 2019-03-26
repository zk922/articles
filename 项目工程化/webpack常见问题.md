# webpack问题总结

## 一、基础部分
1. output中filename，hotUpdateChunkFilename和chunkFilename分别指代什么？
2. output.publicPath的作用？
3. output中library和libraryTarget作用？
4. module.noParse作用？
5. html-webpack-plugin和html-loader分别的作用？
6. file-loader和extract-loader的作用？

## 二、实际使用中的问题
1. 如何提高打包性能
2. 如何将公共的静态资源单独打包？
3. 如何将第三方静态组件分离出来不打包？
4. html-loader，html-webpack-plugin冲突的问题怎么解决？
5. webpack多页面项目中，html复用的实现方案有什么？