# projectzerorn-umeng-analysis

详细文档 参考 http://dev.umeng.com/analytics/h5/react-native%E9%9B%86%E6%88%90%E6%96%87%E6%A1%A3
友盟应用统计- react-native

安装 ` npm install --save projectzerorn-umeng-analysis@https://github.com//projectzerorn/projectzerorn-umeng-analysis.git`
##### **android项目引入本库**
1. 清单文件配置
 ```
     <manifest……>
        <uses-sdk android:minSdkVersion="4"></uses-sdk>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.INTERNET"></uses-permission>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>
    <application ……>
    ……
    <activity ……/>
    <meta-data android:value="YOUR_APP_KEY" android:name="UMENG_APPKEY"></meta-data>
    <meta-data android:value="Channel ID" android:name="UMENG_CHANNEL"/>
    </application>
    </manifest>


 ```
不在manifest里配置友盟的appkey，也可以在Activity中配置：只需在程序启动时的Activity中调用此接口实现:MobclickAgent.startWithConfigure(UMAnalyticsConfig config);

2.**java代码配置**

React Native的android启动入口页面,使用ReactInstanceManager对象添加UmengReactPackage. BaseProjectReactNativeActivity参考代码如下
```
       mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setBundleAssetName("index.android.bundle")
                .setJSMainModuleName("index.android")
                .addPackage(new UmengReactPackage())
                .setUseDeveloperSupport(isReactNativeUseLocalServer
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();

```
React Native的android启动入口页面，onCreate()方法初始化友盟统计,参考代码如下
```
 MobclickAgent.setDebugMode(false);
 MobclickAgent.openActivityDurationTrack(false);
 MobclickAgent.setSessionContinueMillis(1000);
 MobclickAgent.setScenarioType(this,MobclickAgent.EScenarioType.E_UM_NORMAL);
```
3.**配置android项目build.gradle**
```
dependencies {
    compile project(':projectzerorn-umeng-analysis')
}
```
4.**配置android项目settings.gradle**
```
include ':projectzerorn-umeng-analysis'
project(':projectzerorn-umeng-analysis').projectDir = new File(rootProject.projectDir, '../../node_modules/projectzerorn-umeng-analysis/android')
```
5.**js代码引用**
跟下面的ios的js代码引用保持一致

##### **iOS项目引入本库(手动引入xcode)**

1.  **导入SDK**

   请在你的工程目录结构中，添加友盟统计框架，在选项TARGETS--> Build Phases-->Link Binary With Libraries-->Add Other，选择文件UMMobClick.framework文件并选择确认；添加系统依赖框架(Framework)和编译器选项 TARGETS-->Build Phases-->Link Binary With Libraries--> + -->CoreTelephony.framework libz.tbd libsqlite.tbd

2. ###  配置 *AppDelegate.m (*代表你的工程名字)

   导入头文件#import "UMMobClick/MobClick.h"

   *AppDelegate.m 的配置主要包括填写Appkey，设置发送策略和填写渠道id三部分，代码示例如下：

   ```
   #import "UMMobClick/MobClick.h"
    ... - (BOOL))application:(UIApplication) *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
   UMConfigInstance.appKey = @"xxxxxxxxxxxxxx...";
   UMConfigInstance.ChannelId = @"App Store";
   UMConfigInstance.eSType = E_UM_GAME; //仅适用于游戏场景，应用统计不用设置        …       
   [MobClick startWithConfigure:UMConfigInstance];//配置以上参数后调用此方法初始化SDK！
    } 

   ```

   UMConfigInstance为SDK参数配置的实例类，只需要将其成员中标注为required的参数赋值，optional的为可选项。

   appKey为开发者在友盟后台申请的应用Appkey（Appkey可在统计后台的 “统计分析->设置->应用信息” 页面查看）； ChannelId的值为应用的渠道标识。默认为 @"App Store"

   ##### **js代码引用**

   ReactNative项目中的js代码中，需要进行统计的页面引用,**BasePage**

   `import Analysis from 'projectzerorn-umeng-analysis';`

   其中this.getPageName()得到的是当前page的名字

   ```
   componentWillMount() {
       Analysis.onPageBegin(this.getPageName());
   }
   componentWillUnmount() {
       Analysis.onPageEnd(this.getPageName());
   }
   ```
