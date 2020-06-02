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

2.	分别添加URL Schemes 
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

```

## 代码使用
在`AppDelegate.h`导入头文件：
```
#import <LYSDK/LYSDK.h>
#import <LYSDK/LYSingletion.h>

```
初始化SDK 在应用的 `Application` ` didFinishLaunchingWithOptions
 ` 方法中 初始化下方代码 ， 参数一是链游玩家平台分配的 `appid` 参数二是链游玩家平台分配的 `key`，
`setDebug` 方法开启sdk debug 和 release模式
```
    [[LYSingletion sharedManager] configurationWithAppid:@" xxx " withKey:@" xxx "];
    [[LYSingletion sharedManager] setDebug:NO];
  ```
##### 登录注册 

```
 导入头文件
 #import <LYSDK/LYSDK.h>
 #import <LYSDK/LYLoginViewController.h>

 需要添加回调代理：LYLoginViewControllerDelegate

 //注册
  LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
  loginVC.pageState = PageStateRegister; //注册
  loginVC.delegate = self; //代理
  //若您的app为横屏，强制竖屏方法 （登录、注册、充值界面仅支持竖屏展示）
  YGNavigationController * nav = [[YGNavigationController alloc]initWithRootViewController:loginVC];
  [self presentViewController:nav animated:YES completion:nil];

  //登录
  LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
  loginVC.pageState = PageStateLogin; //登录
  loginVC.delegate = self;
  //若您的app为横屏，强制竖屏方法 （登录、注册、充值界面仅支持竖屏展示）
  YGNavigationController * nav = [[YGNavigationController alloc]initWithRootViewController:loginVC];
  [self presentViewController:nav animated:YES completion:nil];
  
  //替换背景图方法：
  //竖屏背景图
  loginVC.bgImage = [UIImage imageNamed:@"xxx"];
  
```
##### 登录注册成功或失败回调方法
监听登录和登出事件的回调方法 登录成功会返回 `UserInfo` 失败返回错误原因`error`
```
需要先添加代理：LYLoginViewControllerDelegate

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
##### 支付
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
rechargeVC.delegate = self;//代理
//若您的app为横屏，强制竖屏方法 （登录、注册、充值界面仅支持竖屏展示）
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
 ```       
## 悬浮窗功能
悬浮框提供链游玩家的钱包、礼包、代金券、消息等功能

在所需添加悬浮窗view中添加如下代码
```    
添加头文件：
#import <LYSDK/LYFloatView.h>

//创建悬浮窗
LYFloatView * floatView = [[LYFloatView alloc]init];
floatView.delegate = self;
floatView.userId = @"xxx";//使用时必须绑定您的userid(必填)
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





