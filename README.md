# TechAliPush
集成阿里移动推送

# 集成说明

1.在工程build.gradle：

  allprojects {
      repositories {
        ...
        maven { url 'https://jitpack.io' }
        
         google()
         
        jcenter()
        
        mavenCentral()
        
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/releases/'
        }
      }
    }
    
2.在module的gradle里依赖

  dependencies {
     compile 'com.github.TopTech666:TechAliPush:1.1.5'
   }
   
3.创建自己的接受类MyMessageReceiver 

public class MyMessageReceiver extends MessageReceiver {
    // 消息接收部分的LOG_TAG
    public static final String REC_TAG = "receiver";

    @Override
    public void onNotification(Context context, String title, String summary, Map<String, String> extraMap) {
        // TODO 处理推送通知
        Log.e("MyMessageReceiver", "Receive notification, title: " + title + ", summary: " + summary + ", extraMap: " + extraMap);
    }

    @Override
    public void onMessage(Context context, CPushMessage cPushMessage) {
        Log.e("MyMessageReceiver", "onMessage, messageId: " + cPushMessage.getMessageId() + ", title: " + cPushMessage.getTitle() + ", content:" + cPushMessage.getContent());
    }

    @Override
    public void onNotificationOpened(Context context, String title, String summary, String extraMap) {
        Log.e("MyMessageReceiver", "onNotificationOpened, title: " + title + ", summary: " + summary + ", extraMap:" + extraMap);
    }

    @Override
    protected void onNotificationClickedWithNoAction(Context context, String title, String summary, String extraMap) {
        Log.e("MyMessageReceiver", "onNotificationClickedWithNoAction, title: " + title + ", summary: " + summary + ", extraMap:" + extraMap);
    }

    @Override
    protected void onNotificationReceivedInApp(Context context, String title, String summary, Map<String, String> extraMap, int openType, String openActivity, String openUrl) {
        Log.e("MyMessageReceiver", "onNotificationReceivedInApp, title: " + title + ", summary: " + summary + ", extraMap:" + extraMap + ", openType:" + openType + ", openActivity:" + openActivity + ", openUrl:" + openUrl);
    }

    @Override
    protected void onNotificationRemoved(Context context, String messageId) {
        Log.e("MyMessageReceiver", "onNotificationRemoved");
    }

4.在清单文件里注册MyMessageReceiver 
 <!-- 消息接收监听器 （用户可自主扩展） -->
 
    <meta-data android:name="com.alibaba.app.appkey" android:value="appKey"/> <!-- 请填写你自己的- appKey -->
        <meta-data android:name="com.alibaba.app.appsecret" android:value="appSecret"/> <!-- 请填写你自己的appSecret -->

        <receiver
            android:name="xxx.MyMessageReceiver"//包名+接受者的类名
            android:exported="false" > <!-- 为保证receiver安全，建议设置不可导出，如需对其他应用开放可通过android：permission进行限制 -->
            <intent-filter>
                <action android:name="com.alibaba.push2.action.NOTIFICATION_OPENED" />
            </intent-filter>
            <intent-filter>
                <action android:name="com.alibaba.push2.action.NOTIFICATION_REMOVED" />
            </intent-filter>
            <intent-filter>
                <action android:name="com.alibaba.sdk.android.push.RECEIVE" />
            </intent-filter>
        </receiver>
        
5.Proguard配置

    -keepclasseswithmembernames class ** {
        native <methods>;
    }
    -keepattributes Signature
    -keep class sun.misc.Unsafe { *; }
    -keep class com.taobao.** {*;}
    -keep class com.alibaba.** {*;}
    -keep class com.alipay.** {*;}
    -keep class com.ut.** {*;}
    -keep class com.ta.** {*;}
    -keep class anet.**{*;}
    -keep class anetwork.**{*;}
    -keep class org.android.spdy.**{*;}
    -keep class org.android.agoo.**{*;}
    -keep class android.os.**{*;}
    -dontwarn com.taobao.**
    -dontwarn com.alibaba.**
    -dontwarn com.alipay.**
    -dontwarn anet.**
    -dontwarn org.android.spdy.**
    -dontwarn org.android.agoo.**
    -dontwarn anetwork.**
    -dontwarn com.ut.**
    -dontwarn com.ta.**

6.在应用中注册和启动移动推送,在application里初始化
    
    public class MyApplication extends Application {
    private static final String TAG = "MyApplication";

    @Override
    public void onCreate() {
        super.onCreate();
         PushUtils.initCloudChannel(this,"xxx","xxx");//阿里云注册的appKey和appSecret
        // PushUtils.initThirdCloudChannel(this,"xxx","xxx");//必须在推送初始化之后，第三方推送：华为和小米
        // PushUtils.initGcmCloudChannel(this,"xxx","xxx");//初始化gcm服务sendId/applicationId为步骤获得的参数
    } 
}

7.集成第三方辅助通道（小米和华为）
在应用application里调用

 PushUtils.initThirdCloudChannel(this,"xxx","xxx");//必须在推送初始化之后，即  PushUtils.initCloudChannel()方法后面，第三方推送：华为和小米

8.混淆
    # 小米通道
    -keep class com.xiaomi.** {*;}
    -dontwarn com.xiaomi.**
    # 华为通道
    -keep class com.huawei.** {*;}
    -dontwarn com.huawei.**
  


