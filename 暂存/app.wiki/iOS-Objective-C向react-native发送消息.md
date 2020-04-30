在Xcode中创建一个类 

引入#import <React/RCTBridgeModule.h> #import <React/RCTEventEmitter.h>且使用 接口实现RCTBridgeModule协议，

实现方法 [self sendAppEventWithName:eventName body:eventBody]; （eventName是方法名，eventBody是字典参数）

我们需要复写supportedEvents方法

- (NSArray<NSString *> *)supportedEvents
{

  return @[eventName];

}

如项目中 RNEventEmitter.h RNEventEmitter.m

当rn想要接收来自oc的消息时，需要添加监听者

/*

  OCClassName：消息发送类类名

  eventBody：oc传过来的消息实体，可作为参数在coding处使用

  eventName：string类型，需要和oc类中定义的一致

*/

var NativeAppEventEmitter = NativeModules.OCClassName;

var eventObject = new NativeEventEmitter(NativeAppEventEmitter);

this.listener = eventObject.addListener(
    'eventName', (eventBody) => {
      // coding
    }
);