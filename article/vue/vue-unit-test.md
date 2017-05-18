# vue项目单元测试

## 从vue[官方命令行工具](https://cn.vuejs.org/v2/guide/installation.html)快速搭建大型单页应用

## 直接npm run unit此时会报一些错
* 1 phantomjs-prebuilt@2.1.14 install时会报错
* 2 test/unit/karma.conf.js文件中无法使用browsers: ['PhantomJS']

## 做如下修改
* 1 安装chrome浏览器 npm install --save-dev karma-chrome-launcher
* 2 test/unit/karma.conf.js文件修改browsers: ['Chrome']
* 3 node npm 安装最新版 node v6.10.3  npm v3.10.10
* 4 npm install --save-dev phantomjs-prebuilt@2.1.14

## 必须保证test/unit/specs下有单元测试的文件
![test demo](https://github.com/diDiAo1/blog/blob/master/images/vue/test-demo.png)

## npm run unit
