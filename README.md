## 链游玩家SDK  iOS版本
## 介绍
链游玩家SDK提供登录注册、支付以及应用内悬浮框功能。
## 用法
将LYSDK.framework 、 LYResource.bundle 、ThirdLib拖进项目
```
在Build Settings , Other Linker Flags 加入 -ObjC
```
在info.plist 如下配置：

1.配置网络
```
App Transport Security Settings
Allow Arbitrary Loads    YES

```

2.分别添加URL Schemes 
```
www.bgplayer.vip  和  www.lywj.ihangwei.com
```

在AppDelegate 加入下列代码
```
//微信支付回调
-(BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{
    //需和scheme 配置相同
    if ([url.scheme isEqualToString:@"www.lywj.ihangwei.com"] || [url.scheme isEqualToString:@"www.bgplayer.vip"]) {
        [[NSNotificationCenter defaultCenter] postNotificationName:@"H5PayFinishedGoback" object:nil];
        return YES;
    }
    return NO;
}

AppDelegate.h 内添加
@property (strong, nonatomic) UIWindow *window;

```

## 代码使用
在`AppDelegate.h`导入头文件：
```
#import <LYSDK/LYSDK.h>
#import <LYSDK/LYSingletion.h>

```
初始化SDK 在应用的 `Application` ` didFinishLaunchingWithOptions
 ` 方法中 初始化下方代码 ， 参数一是链游玩家平台分配的 `appid` 参数二是链游玩家平台分配的 `key`，
`setDebug` 方法开启sdk debug环境（测试环境） 和 release模式（正式环境）， `isShowAntiAddictionUI`是否展示防沉迷界面 （YES-展示SDK自带防沉迷界面，NO-自定义防沉迷界面，如不需要此功能可随意填写。 防沉迷回调在下方悬浮框处。）
```
    [[LYSingletion sharedManager] configurationWithAppid:@" xxx " withKey:@" xxx " isShowAntiAddictionUI:YES];
    [[LYSingletion sharedManager] setDebug:NO];
```

如果您的项目，选择接入防沉迷功能，无论您是否需要自定义防沉迷界面，都需添加以下代码
```
//进入前台
- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the active state; here you can undo many of the changes made on entering the background.
    [[LYSingletion sharedManager] timeScoketDisconnectIsEnterBackGroundUI:NO];
}

//进入后台
- (void)applicationWillTerminate:(UIApplication *)application {
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
    [[LYSingletion sharedManager] timeScoketDisconnectIsEnterBackGroundUI:YES];
}

```

  
  
## 登录注册 

```
 导入头文件
 #import <LYSDK/LYSDK.h>
 #import <LYSDK/LYSDKLoginViewController.h>

 需要添加回调代理：LYSDKLoginViewControllerDelegate

 //注册
 LYSDKLoginViewController * loginVC = [[LYSDKLoginViewController alloc]init];
 loginVC.pageState = PageStateRegister; //注册
 loginVC.delegate = self;
 YGNavigationController * nav = [[YGNavigationController alloc]initWithRootViewController:loginVC];
 nav.modalPresentationStyle=UIModalPresentationFullScreen;
 [self presentViewController:nav animated:YES completion:nil];

 //登录
 LYSDKLoginViewController * loginVC = [[LYSDKLoginViewController alloc]init];
 loginVC.pageState = PageStateLogin; //登录
 loginVC.delegate = self;
 YGNavigationController * nav = [[YGNavigationController alloc]initWithRootViewController:loginVC];
 [self presentViewController:nav animated:YES completion:nil];
  
```
##### 登录注册成功或失败回调方法
监听登录和登出事件的回调方法 登录成功会返回 `UserInfo` 失败返回错误原因`error`
```
需要先添加代理：LYSDKLoginViewControllerDelegate

//登录/注册 成功
-(void)LYLoginSucceedWithInfo:(NSDictionary *)info{
}

//登录/注册 失败
- (void)LYFLoginFailWithError:(NSError *)error{
}

```
##### 自动登录 
```
如不需调用登陆接口直接登陆的情况下需调用此接口验证账号，游戏方根据返回的user_id获取该用户的游戏信息，返回信息中user_id为空的情况下需要调用绑定游戏方userid接口绑定双方关系

[[LYSingletion sharedManager] autoLoginVerifysuccess:^(NSDictionary * _Nonnull userInfo) {
    
} failure:^(NSError * _Nonnull error, NSInteger code, NSString * _Nonnull message) {
    //如code=202代表账号被封 400未登录过
}];

```
##### 退出登录 
```
[[LYSingletion sharedManager] loginOut];
```
## 支付
支付需传商品价格 和 商品描述
```
添加头文件：
#import <LYSDK/LYRechargeViewController.h>
#import <LYSDK/YGNavigationController.h>

//充值
//操作此步骤前，请先登录
LYRechargeViewController * rechargeVC = [[LYRechargeViewController alloc]init];
rechargeVC.goodsName = @"2000元宝";//商品描述
rechargeVC.goodsPrice = @"0.01"; //商品价格
rechargeVC.extra = @""; //额外信息 （此处传什么，支付成功后就会返回给你们什么）
rechargeVC.delegate = self;//代理
YGNavigationController * nav = [[YGNavigationController alloc]initWithRootViewController:rechargeVC];
[self presentViewController:nav animated:YES completion:nil];

```
##### 支付结果的回调
需要设置代理LYRechargeDelegate
```
//支付成功
-(void)LYRechargeDelegatewithSucceed:(NSDictionary *)detailInfo{
}
//支付失败
-(void)LYRechargeDelegatewithFail:(NSError *)error{
}

//可选代理
//防沉迷 充值年龄未满18岁提示
-(void)LYRechargeDelegateAntiAddictionWithState:(NetworkCodeState )state; 

//code 数据返回解析
typedef NS_ENUM(NSInteger,NetworkCodeState)
{
    NETWORKCODE_AGE_EIGHT = 211,//充值-未满8岁
    NETWORKCODE_AGE_SIXTEEN = 212,//充值-8到16岁
    NETWORKCODE_AGE_EIGHTEEN = 213,//充值-16到18岁
};

 ```       
##### 验证充值状态
可根据自己项目需求选择性设置，此方法使用前，需要先登录
需传入参数`amount`充值金额。 ` Success` 方法返回成功回调 ， `Failure` 方法返回错误回调。
其中code字段：200验证通过，211，212，213限制充值，其他为错误信息，
```
//此方法使用前，需要先登录
[[LYSingletion sharedManager] checkPayStatewithamount:0.01 Success:^(id  _Nonnull responseObject) {
    
} Failure:^(NSError * _Nonnull error, NSInteger code, NSString * _Nonnull message) {
   
}];

``` 
其中`responseObject`返回参数示例：
```
{
"code":"200",
"data":null,
"message":"验证通过"
}
{
"code":"211",
"data":null,
"message":"网络游戏企业不得为未满8岁的用户提供游戏付费服务"
}

{
"code":"212",
"data":null,
"message":"8周岁以上未满16周岁的用户，单次充值不得超过50元人名币；每月充值金额累计不得超过200元人名币。"
}

{
"code":"213",
"data":null,
"message":"16周岁以上未满18周岁的用户，单次充值不得超过100元人名币；每月充值金额累计不得超过400元人名币。"
}

```
 
## 悬浮窗功能
悬浮框提供链游玩家的钱包、礼包、代金券、消息、游戏资产等功能

在所需添加悬浮窗view中添加如下代码
```    
添加头文件：
#import <LYSDK/LYFloatView.h>

//创建悬浮窗
LYFloatView * floatView = [[LYFloatView alloc]init];
floatView.delegate = self;
[self.view addSubview:floatView];

```    
## 悬浮窗回调
``` 
添加代理：LYFloatViewDelegate

//悬浮框登录/注册成功
-(void)LYFloatViewLoginSucceedWithInfo:(NSDictionary *)info{
    
}
//悬浮框登录/注册失败
-(void)LYFloatViewLoginFailWithError:(NSError *)error{
    
}

//悬浮框 -- 退出账号成功
-(void)LYFloatViewLogoutSucceed{
   
}

//可选代理
-(void)LYFloatViewShareClick;//分享功能
-(void)LYFloatViewGameOutClick;//退出游戏功能
-(void)LYFloatViewRefreshClick;//刷新游戏功能
-(void)LYFloatViewAntiAddictionClickWithOfflineState:(AntiAddictionState )state;//防沉迷触发

//防沉迷触发解析
typedef NS_ENUM(NSInteger,AntiAddictionState)
{
    TOTAL_HOUR_MIDDLE_NIGHT = 1,//每日22时至次日8时，网络游戏企业不得以任何形式为未成年人提供游戏服务。
    TOTAL_HOUR_THREE = 2,//您累计在线已满3小时，将强制下线。
    TOTAL_HOUR_ONE_POINT_FIVE = 3,//您累计在线已满1.5小时，将强制下线。
};

``` 

 
## 冲突
如果有三方库冲突，请直接移除ThirdLib文件夹中对应的三方库 

## 报错
（Xcode11.1为例）

1.Project Unity-iPhone | Configuration Debug \ destination 'xxx'的 iPhone |SDK 13.1
error: "Unity-iPhone" requires a provisioning profile. Select a provisioning profile in the Signing & Capabilities editor. (in target 'Unity-iPhone' from project 'Unity-iPhone')
"Unity-iPhone" requires a provisioning profile. Select a provisioning profile in the Signing & Capabilities editor.

解决方法：Signing & Capabilities  -> Signing -> 勾选 Automatically manage signing -> Team (选个证书)

2.Command PhaseScriptExecution failed with a nonzero exit code
/Users/admin/Desktop/SDKProject/gameTest/IOS/DerivedData/Unity-iPhone/Build/Intermediates.noindex/Unity-iPhone.build/Debug-iphoneos/Unity-iPhone.build/Script-033966F41B18B03000ECD701.sh: line 2: /Users/admin/Desktop/SDKProject/gameTest/IOS-1/MapFileParser.sh: Permission denied

解决方法：打开终端，1.cd 项目路径 ,2.chmod +x MapFileParser.sh           (chmod +x : 给执行权限)

3.Cannot use '@try' with Objective-C exceptions disabled

解决方法： 
Build Settings -> Apple Clang - Language - Objective-C -> Enable Objective-C Exceptions -> 改为YES

4.A parameter list without types is only allowed in a function definition
  Expected ';' at end of declaration
  insert ';'
  
解决方法：Build Settings -> Apple Clang - Language -> C Language Dialect -> C99改为GNU99

5.ld: warning: arm64 function not 4-byte aligned: _unwind_tester from /Users/admin/Desktop/SDKProject/gameTest/IOS/Libraries/libiPhone-lib.a(unwind_test_arm64.o)
Undefined symbols for architecture arm64:
  "_vImageBuffer_InitWithCGImage", referenced from:
      -[UIImage(Transform) sd_blurredImageWithRadius:] in UIImage+Transform.o
  "_vImageBuffer_Init", referenced from:
      -[UIImage(Transform) sd_blurredImageWithRadius:] in UIImage+Transform.o
  "_vImageBoxConvolve_ARGB8888", referenced from:
      -[UIImage(Transform) sd_blurredImageWithRadius:] in UIImage+Transform.o
  "_vImageCreateCGImageFromBuffer", referenced from:
      -[UIImage(Transform) sd_blurredImageWithRadius:] in UIImage+Transform.o
  "_vImageVerticalReflect_ARGB8888", referenced from:
      -[UIImage(Transform) sd_flippedImageWithHorizontal:vertical:] in UIImage+Transform.o
  "_vImageHorizontalReflect_ARGB8888", referenced from:
      -[UIImage(Transform) sd_flippedImageWithHorizontal:vertical:] in UIImage+Transform.o
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)

解决方法：Build Phases -> Link Binary Libraries -> 添加Accelerate.framework





