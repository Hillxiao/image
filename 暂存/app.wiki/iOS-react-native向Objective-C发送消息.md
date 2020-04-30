在Xcode 创建一个类

引入#import <React/RCTBridgeModule.h> 且使用 <RCTBridgeModule> 接口

实现RCTBridgeModule协议，类需要包含RCT_EXPORT_MODULE()宏(这个宏也可以添加一个参数用来指定在Javascript中访问这个模块的名字

将oc的函数导出给js进行调用，那么就需要进行声明。声明通过RCT_EXPORT_METHOD()宏来实现

调用成功后，我们输出这两个传来的值到控制台 （注意有些操作需要使用主线程）

主线程实现方法 

dispatch_async(dispatch_get_main_queue(), ^{
  // 需要实现的方法
});

如项目中 RNBridgeManager.h RNBridgeManager.m 文件

react-native JS代码

import { NativeModules } from "react-native";

export default NativeModules.xxx    (xxx是OC的类方法名)

js 调用 OC 方法

xxx.aaa() (xx是OC的类方法名,aaa是在OC中实现的方法)
