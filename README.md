## 链游玩家SDK  iOS版本
## 介绍
链游玩家SDK提供登录注册、支付以及应用内悬浮框功能。
## 用法
将LYSDK.framework 、 LYResource.bundle 、ThirdLib拖进项目
在Other Linker Flags 加入 –ObjC
在info.plist 如下配置：
1.	App Transport Security Settings
Allow Arbitrary Loads    YES
2.	分别添加URL Schemes 
www.bgplayer.vip  和  www.lywj.ihangwei.com
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
   //注册
    LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
    loginVC.pageState = PageStateRegister; //注册
    UINavigationController * nav = [[UINavigationController alloc]initWithRootViewController:loginVC];
    [self presentViewController:nav animated:YES completion:nil];

  //登录
  LYLoginViewController * loginVC = [[LYLoginViewController alloc]init];
  loginVC.pageState = PageStateLogin; //登录
  UINavigationController * nav = [[UINavigationController alloc]initWithRootViewController:loginVC];
  [self presentViewController:nav animated:YES completion:nil];

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
##### 事件回调 
监听登录和登出事件的回调方法 登录成功会返回 `UserInfo` 失败返回错误原因
 ```
       loginVC.loginSucceedblock = ^(NSDictionary * _Nonnull userInfo) {
            };
       loginVC.loginFailblock = ^(NSError * _Nonnull error) {
            };
 ```
 支付结果的回调，需要设置代理LYRechargeDelegate
```
       -(void)LYRechargeDelegatewithSucceed:(NSDictionary *)detailInfo{
}

-(void)LYRechargeDelegatewithFail:(NSError *)error{
}
 ```       
## 冲突
如果有三方库冲突的话，请直接移除ThirdLib文件夹中对应的三方库 

