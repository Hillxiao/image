在原生Lib目录下导入 WechatSDk1.8.6.1 (目前最新版本含微信支付)

微信支付流程：选择购买产品 -> 后台生成订单把调起微信支付的参数返回 -> react-native把订单参数传给原生OC由原生OC调起微信支付 -> 支付后把支付状态返回react-native -> react-native处理支付状态 

实现： 
在AppDelegate.m 文件注册

 //向微信注册

[WXApi registerApp:@"wxa4ed0c61e9dc1129" universalLink:@"https://epay.96533.com/"];

```
调起微信支付
在WeChatPayManager.m文件中
/**
 * RN 调起 原生 微信支付
 * object 微信支付所需的参数
 */
RCT_EXPORT_METHOD(weChatPay:(NSDictionary *)object){
  dispatch_async(dispatch_get_main_queue(), ^{
    if (![WXApi isWXAppInstalled]) {
        [MBProgressHUD showTipStr:@"检测到您还没有安装微信"];
        return;
    }
    WeChatPayModel *model = [[WeChatPayModel alloc] init];
    if ([object isKindOfClass:[NSDictionary class]] && [[object allKeys] containsObject:@"appid"]) {
        [model setValuesForKeysWithDictionary:object];
        PayReq *req   = [[PayReq alloc] init];
         //由用户微信号和AppID组成的唯一标识，用于校验微信用户
        req.openID = model.appid;
         // 商家id，在注册的时候给的
        req.partnerId = model.partnerid;
         // 预支付订单这个是后台跟微信服务器交互后，微信服务器传给你们服务器的，你们服务器再传给你
        req.prepayId  = model.prepayid;
         // 根据财付通文档填写的数据和签名
         //这个比较特殊，是固定的，只能是即req.package = Sign=WXPay
        req.package   = model.package;
         // 随机编码，为了防止重复的，在后台生成
        req.nonceStr  = model.noncestr;
         // 这个是时间戳，也是在后台生成的，为了验证支付的
        req.timeStamp = model.timestamp.intValue;
         // 这个签名也是后台做的
        req.sign = model.sign;
         //发送请求到微信，等待微信返回onResp
       [WXApi sendReq:req completion:^(BOOL success) {
       }];
    }
  });
}


```
支付完成回调 在AppDelegate.m
```
#pragma mark -- 微信支付回调 回调码穿到react-native
-(void) onResp:(BaseResp*)resp{
  if([resp isKindOfClass:[BaseResp class]]){
    if (!_evebtEmitter) {
      _evebtEmitter = [[RNEventEmitter allocWithZone:NULL] init];
    }
    dispatch_async(dispatch_get_main_queue(), ^{
      [self.evebtEmitter weChatPayBack:[NSString stringWithFormat:@"%d",resp.errCode]];
    });
  }
}
```