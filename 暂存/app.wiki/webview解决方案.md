# Webview业务细解

## 问题起源

我们有一些业务,如旧货车金融,可能会使用到hybird,于是考虑在RN上引入webview。
但是官方不再维护webview组件，是以第三方库的形式引入 。见下说明：
```
  Warning Please use the react-native-community/react-native-webview fork of this component instead. To reduce the surface area of React Native, <WebView/> is going to be removed from the React Native core. For more information, please read The Slimmening proposal.
```

考虑引入社区维护的react-native-webview [文档地址](https://github.com/react-native-community/react-native-webview) ，以减少以后项目的维护成本。

先入引入react-native-webview,发现在项目启动时,Rn有很多脚本直接使用yarn包管理工具(yarn是Rn他们自己开发的)。我们跟随fackbook使用yarn,暂时弃用npm的原因是它需要很多手动操作, 详细安装`npm -g install yarn`.

引入过程会有一些问题,如

>`Duplicate module name: react-native`
遇到此问题,检查引入两次的package.json文件,删除掉其中一份,然后清掉缓存重启
`react-native start --reset-cache`


在起服务的过程中,遇到一些问题
因为现在的手机相比以前性能较好,不需要再携带webview新版本，直接使用原生携带的webview即可。如果要用原生功能，我们需要 `react-native link` 来链接,在安卓和ios上加入原生代码,以支持Rn调用底层.
如果项目忘记了链接,会有一些没找到RNCWebView类等一些问题,项目未编译的代码在
`node_modules/react-native-webview/android``node_modules/react-native-webview/ios`  
其他第三方库引入类些道理,如有自动link,不熟悉项目避免手动link.

在link的过程中,会对现在安卓,ios的文件有修改,其中有使用到了androidx,我们还需要对
   `android/gradle.properties`
   文件进行添加两行
   `android.useAndroidX=true
   android.enableJetifier=true`
   
   link完之后 ,汇总一下,有以下文件被修改
   ```
   android/app/build.gradle
   android/app/src/main/java/com/rn_app/MainApplication.java
   android/gradle.properties
   android/settings.gradle
   ios/rn_app.xcodeproj/project.pbxproj
   ```

#跳转组件navigation设计
横向比较week和hybird,RN突出的优势在跳转的时候比较优秀
我们项目不可避免使用很多个第三方库.  
如果和不同项目同时引入,会有一个新的问题,就是webview中使用到了androidX,这个方案是谷歌为了统一以前旧有的support版本各种混乱出现.
旧有的项目是以support.v4或者support.v7来写的,新项目谷歌官方推荐使用androidX. 在这知识背景上, 我们还需要兼容新旧的代码.
解决方案  
`android/gradle.properties` 文件的 `enableJetifier`设计为`false`