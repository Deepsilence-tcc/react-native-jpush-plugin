# react-native-jpush-plugin

###Android Usage

- 使用命令行在你的React Native Project目录中安装：

```
npm install jpush-react-native --save

rnpm link jpush-react-native
```

- 使用Android Studio import你的React Native应用（选择你的React Native应用所在目录下的android文件夹即可）

- 修改android项目下的setting.gradle配置：

> setting.gradle

```
include ':app', ':jpush-react-native'
project(':jpush-react-native').projectDir = new File(rootProject.projectDir, '../node_modules/jpush-react-native/android')

```

- 修改app下的build.gradle配置：

> your react native project/android/app/build.gradle

```
dependencies {
    compile fileTree(dir: "libs", include: ["*.jar"])
    compile project(':jpush-react-native')
    compile "com.facebook.react:react-native:+"  // From node_modules
}
```

- 现在重新sync一下项目，应该能看到jpush-react-native作为一个android Library项目导进来了

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin1.png)

- 打开jpush-react-native的build.gradle文件，修改相关配置：

> jpush-react-native/android/build.gradle

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin2.png)

将此处的yourAppKey替换成你在官网上申请的应用的AppKey

- 打开app下的MainActivity，在ReactInstanceManager的build方法中加入JPushPackage：

> app/MainActivity.java

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin3.png)

- 在JS中import JPushModule，然后即调用相关方法：
```
import JPushModule from 'jpush-react-native';

...

componentDidMount() {
    JPushModule.addReceiveCustomMsgListener((message) => {
      this.setState({pushMsg: message});
    });
    JPushModule.addReceiveNotificationListener((message) => {
      console.log("receive notification: " + message);
    })
  }

  componentWillUnmount() {
    JPushModule.removeReceiveCustomMsgListener();
    JPushModule.removeReceiveNotificationListener();
  }
```

关于JPushModule的具体方法可以参考jpush-react-native文件夹下的index.js文件，此处将方法罗列如下：

- initPush()
- getInfo(map)
```
JPushModule.getInfo((map) => {
      this.setState({
            appkey: map.myAppKey,
            imei: map.myImei,
            package: map.myPackageName,
            deviceId: map.myDeviceId,
            version: map.myVersion
      });
    });
```
- stopPush()
- resumePush()
- setTag(tag)
- setAlias(alias)
- setStyleBasic()
- setStyleCustom()
- addReceiveCustomMsgListener(callback)
- removeReceiveCustomMsgListener()
- addReceiveNotificationListener()
- removeReceiveNotificationListener()


####iOS Usage
- 打开iOS工程，在rnpm link 之后，RCTJPushModule.xcodeproj 工程会自动添加到 Libraries 目录里面
- 在iOS工程target的Build Phases->Link Binary with Libraries中加入libz.tbd、CoreTelephony.framework、Security.framework
- 在RCTJPushModule.xcodeproj工程 -> RCTJPushModule.h 文件中 填写自己的appkey、channel、和isProduction
```
static NSString *appKey = @"";     //填写appkey
static NSString *channel = @"";    //填写channel
static BOOL isProduction = false;  //填写isProdurion  平时测试时为false ，生产时填写true
```
- 在AppDelegate.m 的didFinishLaunchingWithOptions 方法里面添加如下代码
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
if ([[UIDevice currentDevice].systemVersion floatValue] >= 8.0) {
    //可以添加自定义categories
    [JPUSHService registerForRemoteNotificationTypes:(UIUserNotificationTypeBadge |
                                                      UIUserNotificationTypeSound |
                                                      UIUserNotificationTypeAlert)
                                          categories:nil];
  } else {
    //iOS 8以前 categories 必须为nil
    [JPUSHService registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge |
                                                      UIRemoteNotificationTypeSound |
                                                      UIRemoteNotificationTypeAlert)
                                          categories:nil];
  }
  
  [JPUSHService setupWithOption:launchOptions appKey:appKey
                        channel:channel apsForProduction:isProduction];
}
```
- 在AppDelegate.m 的didRegisterForRemoteNotificationsWithDeviceToken 方法中添加 [JPUSHService registerDeviceToken:deviceToken]; 如下所示
```
- (void)application:(UIApplication *)application
didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  [JPUSHService registerDeviceToken:deviceToken];
}
```
---
贡献者列表
- [bang88](https://github.com/bang88)




