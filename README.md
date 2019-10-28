## 链游玩家SDK  iOS版本
## 介绍
链游玩家SDK提供登录注册、支付以及应用内悬浮框功能。
## 用法
将LYSDK.framework 、 LYResource.bundle 、ThirdLib拖进项目
在Other Linker Flags 加入 –ObjC
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
初始化SDK 在应用的 `Application` ` didFinishLaunchingWithOptions
 ` 方法中 初始化下方代码 ， 参数一是链游玩家平台分配的 `appid` 参数二是链游玩家平台分配的 `key`，
`setDebug` 方法开启sdk debug 和 release模式
```
    [[LYSingletion sharedManager] configurationWithAppid:@" xxx " withKey:@" xxx "];
    [[LYSingletion sharedManager] setDebug:NO];
  ```
##### 登录注册 
```
 需要添加回调代理：LYLoginViewControllerDelegate

 //注册
  LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
  loginVC.pageState = PageStateRegister; //注册
  loginVC.delegate = self; //代理
  UINavigationController * nav = [[UINavigationController alloc]initWithRootViewController:loginVC];
  [self presentViewController:nav animated:YES completion:nil];

  //登录
  LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
  loginVC.pageState = PageStateLogin; //登录
  loginVC.delegate = self;
  UINavigationController * nav = [[UINavigationController alloc]initWithRootViewController:loginVC];
  [self presentViewController:nav animated:YES completion:nil];

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

##### 退出登录 
```
[[LYSingletion sharedManager] loginOut];
```
##### 支付
支付需传商品价格 和 商品描述
```
//充值
//操作此步骤前，请先登录
LYRechargeViewController * rechargeVC = [[LYRechargeViewController alloc]init];
rechargeVC.goodsName = @"2000元宝";//商品描述
rechargeVC.goodsPrice = @"0.01"; //商品价格
UINavigationController * nav = [[UINavigationController alloc]initWithRootViewController:rechargeVC];
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
//创建悬浮窗
LYFloatView * floatView = [[LYFloatView alloc]init];
loatView.delegate = self;
[self.view addSubview:floatView];
```       
 
## 冲突
如果有三方库冲突的话，请直接移除ThirdLib文件夹中对应的三方库 

