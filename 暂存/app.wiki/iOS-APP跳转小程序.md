开发前需下载iOS开发工具包（SDK），可在“资源中心-开发资源-资源下载-iOS资源下载”按照指引操作。
https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419319164&token=&lang=zh_CN

由于RN的函数声明周期在iOS的子线程运行，不能实现通过微信提供SDK的API直接调起小程序。需要通过桥接文件跳转原生页面跳转，流程如下：

1、`AppDelegate`里面注册微信小程序id 
```
// wxa4ed0c61e9dc1129 为 e行卡小程序的appID
[WXApi registerApp:@"wxa4ed0c61e9dc1129" enableMTA:NO];
```


2、创建桥接文件`[MiniProjectManager]`，导出桥接模块`RCT_EXPORT_MODULE()`，编写函数供`NativeModule`模块调用
```
// 导出桥接模块
RCT_EXPORT_MODULE()

// 提供接口方法供RN模块调用
RCT_EXPORT_METHOD(open){
  dispatch_async(dispatch_get_main_queue(), ^{
    [[NSNotificationCenter defaultCenter]postNotificationName:@"openMiniProjectNotification" object:nil];
  });
}
```
3、编写根视图控制器，继承`UIViewController`，在构造函数注册通知`openMiniProjectNotification`，通过原始`userID:@"gh_53ef9166dbcd"`;  和指定小程序页面如`“pages/service/index”`，以及指定小程序的类型，即可调起跳转小程序
```
- (void)openMiniProject:(NSNotification *)noti{
  WXLaunchMiniProgramReq *launchMiniProgramReq = [WXLaunchMiniProgramReq object];
  launchMiniProgramReq.userName = @"gh_53ef9166dbcd";  //拉起的小程序的username
  launchMiniProgramReq.path = @"pages/service/index";    //拉起小程序页面的可带参路径，不填默认拉起小程序首页
  launchMiniProgramReq.miniProgramType = 0; //拉起小程序的类型
  [WXApi sendReq:launchMiniProgramReq];
}
```