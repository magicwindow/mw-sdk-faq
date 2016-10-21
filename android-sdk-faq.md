#魔窗Android SDK FAQ

FAQ分类
---

* QA[魔窗位](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#魔窗位)
   * Qa1.魔窗位活动不能展示
* QB[分享](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#分享)
   * Qb1.分享弹框缺少图标
   * Qb2.不能分享
   * Qb3.Jar包重复（暂未集成新浪微博分享，忽略此步）
* QC[短链接](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#短链接)
   * Qc1.短链内的参数值能动态修改么？
   * Qc2.App安装的前提下，短链依旧前往下载App页面。
   * Qc3.App安装的前提下，短链只能打开首页，未能进入具体页面。
   * Qc4.通过短链进入具体页面后，清除数据再次打开App，依旧进入短链对应的具体页面。
   * Qc5.mLink短链动态参数如何传递中文？
   * Qc6.如果使用注解，怎么在App中获取参数？
* QD[mLink](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#mlink)
   * Qd1.App未安装时，通过短链跳转到下载页面，安装后，第一次打开未能实现场景还原。
   * Qd2.通过mLink跳转直达的页面，如何做到“先显示启动动画，然后再做相应跳转”
   * Qd3.通过mLink跳转直达的页面，如何做到“返回时进入首页，而不是退出程序”
   * Qd4.跳转到具体页面后，如何获取动态参数。
   * Qd5.提示MLink内的defaultMLinkCallback持有activity导致内存泄露。
   * Qd6.mLink集成跳转到相应activity时黑屏。
   * Qd7.App卸载后再安装，再次进入具体页面，或者没点击过短链的手机也出现了场景还原。
   * Qd8.如果想实现用户登录后再场景还原，需要怎么做？
* QE[应用宝](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#应用宝)
   * Qe1.集成了应用宝跳转，但是从微信打开时没有直接跳转，或者依旧会显示中间页,没有直接打开App。
   * Qe2.集成了应用宝跳转，但是手机上没有安装应用宝怎么办？
   * Qe3.程序在后台时，短链跳转无法跳转到具体页。
   * Qe4.如何开启应用宝跳转
   * Qe5.开启了应用宝跳转，为何getIntent().getData()为空
   * Qe6.应用打开具体页面后，又重新打开了首页。
   * Qe7.checkYYB()是如何实现的？为何YYBCallback只有一个onFailed()？
* QF[其他](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#其他)

魔窗位
===

###Qa1.魔窗位活动不能展示
A:如果出现此问题，请按照以下步骤排查。<br>
1. AndroidManifest.xml内正确填写魔窗APP_ID以及MWActivity等。<br>
2. 魔窗后台正确开启活动，并投放到相应的魔窗位<br>
3. 确保代码中MWImageView绑定正确的魔窗位key<br>
4. 正确集成Session

分享
===
###Qb1.分享弹框缺少图标
A:将assets文件夹下的文件拷贝到相应文件夹即可。

###Qb2.不能分享
A:现阶段魔窗支持微信分享。<br>
1）前往微信开放平台申请微信AppID，
[参考文档](https://open.weixin.qq.com)<br>
注意：包名以及应用签名不要写错。<br>
2）将微信AppID填写在魔窗后台->帐号设置->应用管理->设置分享平台<br>

###Qb3.Jar包重复（暂未集成新浪微博分享，忽略此步）
A:如果显示微信分享等jar包重复。（特别如果集成了shareSDK，会报微博的jar包重复）<br>
1. 将微信jar包libmma.jar删除。<br>
2. 初始化魔窗SDK时，启用shareSDK<br>
```Java
  MWConfiguration config = new MWConfiguration(this);
  config.setSharePlatform(MWConfiguration.SHARE_SDK);
```

短链接
====
###Qc1.短链内的参数值能动态修改么？<br>
A:可以，短链支持将参数的动态值作为 query 放在后面<br>
例如:http://a.t.mlinks.cc/ANax?id=12345<br>

###Qc2.App安装的前提下，短链依旧前往下载App页面。<br>
A:换一台其他品牌的手机验证或者换一个浏览器进行验证比如UC、QQ浏览器，因为有些机型原生浏览器不支持scheme。如果依旧不行，则排查是否Scheme配置错误。<br>
遇到这个问题的时候，我们的第一反应是检查Scheme有没有配置错误。因为程序未能打开App，所以肯定是集成的初始阶段出了问题。<br>
①检查AndroidManifest.xml内的Scheme是否配置正确。此处比较容易出错的是，后台配置scheme需要”://”，而Android内配置时不需要”://”<br>
②后台填写Scheme和配置mLink服务需要注意，其中Scheme需要用全小写字母。如果有数字，数字不能放在开头。<br>

###Qc3.App安装的前提下，短链只能打开首页，未能进入具体页面。<br>
A:有如下可能原因。 <br>
①	检查register回调有没有写错。（register要写在启动页的onCreate内）<br>
②	检查router入口有没有漏掉或者写错位置。 （router是真正的入口，如果有启动动画等，可以方在动画后。）<br>
③	程序做了混淆，但是没有将魔窗的keep出来。<br>
④	如果开启了应用宝跳转，则检查checkYYB接口有没有漏写，（checkYYB需要放在耗时的启动之后，跟启动首页的startActivity放在一起。）<br>
如果以上都没有错误，我们可以利用Charles抓包，查看dpls/v2接口内有没有正确返回mLink的服务uri。从而判断后台是否配置正确。

###Qc4.通过短链进入具体页面后，清除数据再次打开App，依旧进入短链对应的具体页面。<br>
A:“程序安装后第一次打开，魔窗mLink会跟后台通信实现场景还原。App清除数据后，mLink会判断程序为第一次安装。此时请求后台并匹配成功。所以会进入具体页面”。<br>
用户实际使用时基本不会发生此类情况。属于极小概率事件。<br>

###Qc5.mLink短链动态参数如何传递中文？<br>
A:短链后面如果需要添加中文等字符，需要将相关value值encode一下。<br>

###Qc6.如果使用注解，怎么在App中获取参数？<br>
A:可以用如下的方式获取
```Java
        Intent intent = getIntent();
        if (intent != null) {
            String param = intent.getStringExtra("param");
        }
```
  
MLink
===
###Qd1.App未安装时，通过短链跳转到下载页面，安装后，第一次打开未能实现场景还原。
A:
①新版本SDK需要在初始化结束后手动调用MLink.getInstance(this).deferredRouter()接口。<br>
②router必须要写在启动页。否则安装后，第一次打开无法场景还原，且走了router后就不要再走跳转到首页的逻辑。<br>

###Qd2.通过mLink跳转直达的页面，如何做到“先显示启动动画，然后再做相应跳转”
A:可以在动画结束时再调用router,如下：<br>
```Java
         Uri mLink = getIntent().getData();
         if(mLink!=null){
              MLink.getInstance(this).router(mLink);
         }            
```
###Qd3.通过mLink跳转直达的页面，如何做到“返回时进入首页，而不是退出程序”<br>
A:有两种方法：<br>
方法①<br>
启动页面调用router的地方稍作调整，将跳转到首页的代码放在getIntent().getData()!=null的判断外面：<br>
```Java
@Override
public void onCreate(Bundle savedInstanceState) {
// TODO其他代码

    //跳转到首页
    gotoHome();
    //跳转router调用
    if (getIntent().getData()!=null) {
        MLink.getInstance(this).router(getIntent().getData());
    } else {
        //如果需要应用宝跳转，则调用。否则不需要
        MLink.getInstance(this).checkYYB();
    }
    //跳转后结束当前activity
    finish();
}
```
方法②<br>
可以处理直达页面的返回函数。<br>
我们以跳转页为DetailActivity为例：<br>
第一步，在MLink.getInstance(this).register()函数的回调函数增加一个intent.putExtra("mlink",true);<br>
```Java
MLink.getInstance(this).register("mLink的Key", new MLinkCallback() {
            public void execute(Map<String, String> paramMap, Uri uri, Context context) {
                Intent intent = new Intent(context, DetailActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP);
                if (paramMap != null) {
                    for (Map.Entry<String, String> param : paramMap.entrySet()) {
                        intent.putExtra(param.getKey(), param.getValue());
                    }
                }
                intent.putExtra("mlink",true);
                context.startActivity(intent);

            }
        });        
```
第二步：在DetailActivity内的返回函数内，根据intent.getBooleanExtra("mlink",false)是否为true来跳转到首页。

###Qd4.跳转到具体页面后，如何获取动态参数。
A：例如，我们想获取mw_id这样的动态参数：<br>
①在mLink服务那，如下配置mwdemo://host?mw_id=:mw_id<br>
②配置短链时，可以配置mw_id的默认值，也可以在query直接赋值<br>
例如：http://a.t.mlinks.cc/Aaaa?mw_id=12345<br>
③代码内可以如下获取。<br>
如果利用register，则所有的参数，都放在MLinkCallback回调的Map paramMap内。<br>
如果利用注解，则可用getIntent().getStringExtra("key")获取。

###Qd5.提示MLink内的defaultMLinkCallback持有activity导致内存泄露。
A:register内的回调需要用application的Context，且方法需要用static。具体如下

```
public static void registerForMLinkCallback() {
    MLink mLink = MagicWindowSDK.getMLink();
    mLink.register("mLink服务Key", new MLinkCallback() {
        public void execute(Map<String, String> paramMap, Uri uri, Context context) {
            //跳转
            MLinkIntentBuilder.buildIntent(paramMap, context, DetailActivity.class);

        }
    });
    mLink.register("detail", new MLinkCallback() {
        public void execute(Map<String, String> paramMap, Uri uri, Context context) {

            Toast.makeText(context, "open detail:" + uri, Toast.LENGTH_LONG).show();
            MLinkIntentBuilder.buildIntent(paramMap, context, DetailActivity.class);

        }
    });
    mLink.register("product", new MLinkCallback() {
        public void execute(Map<String, String> paramMap, Uri uri, Context context) {
            Intent intent = new Intent(context, DetailActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP);
            if(paramMap !=null){
                for (Map.Entry<String, String> param : paramMap.entrySet()) {
                    intent.putExtra(param.getKey(), param.getValue());
                }
            }
            context.startActivity(intent);

        }
    });
}
```
或者把registerForMLinkCallback()写在一个单独的工具类中。


###Qd6.mLink集成跳转到相应activity时黑屏
A:第一步：xxx/res/values/styles.xml中加入自定义Activity的Theme，如下所示：<br>
```Java
<style name="Transparent" parent="android:Theme.Light">  
        <!--将Activity的Theme设置成透明-->  
        <item name="android:windowIsTranslucent">true</item>  
    </style>  
```
第二步：在AndroidManifest.xml中将第二个Activity的"android:theme"属性设置成刚才自定义的主题样式。如下所示：<br>
```Java
<activity  
    android:name="com.yutao.customer.CustomerActivity"  
    android:label="@string/app_name"  
    android:theme="@style/Transparent"/>
```

###Qd7.App卸载后再安装，再次进入具体页面，或者没点击过短链的手机也出现了场景还原。
A:场景还原是通过魔窗后台的模糊匹配进行的。而模糊匹配的结果时效是一个小时。也就是说在一个小时内，你重新安装App都会场景还原。
模糊匹配用到的UA主要有分辨率、IP地址等，只要在同一个局域网内点击过短链，会有概率出现没点过短链的手机，第一次安装也会实现场景还原。
用户实际使用的场景内，不会有影响。

###Qd8.如果想实现用户登录后再场景还原，需要怎么做？
A:用户登陆完以后调用一个api:<br>
```Java
    MLink.getInstance(this).deferredRouter();
```
deferredRouter()是在sdk内部会判断App是否需要进行场景还原，并且记录了场景还原的uri scheme。然后如果需要场景还原的话，deferedRounter()会调用类似router()。

应用宝
===
###Qe1.集成了应用宝跳转，但是从微信打开时没有直接跳转，或者依旧会显示中间页,没有直接打开App。
A：<br>
①确保后台打开应用宝开关。魔窗后台将下载链接改为应用宝的地址，并且在mLink服务的高级设置内打开应用宝跳转开关。<br>
②保证应用宝商店里的App跟手机里的App的版本号和包名保持一致<br>

###Qe2.集成了应用宝跳转，但是手机上没有安装应用宝怎么办？
A: 可以在应用宝的H5页面里，点击普通打开，并选择继续，就能打开app了。具体可以看下面的动画。

<img src="images/android-1.gif" width="300" height="450" />

###Qe3.程序在后台时，短链跳转无法跳转到具体页。
A:首先切换一下网络，排除因网络不稳定导致的失败（应用宝跳转需要根据checkYYB()接口去跟后台通信。通过模糊匹配来进行具体页面跳转）。除此之外，可按照以下情况调整：
情况①，在公共Activity的onStart()方法中调用如下代码。
```
public class BaseActivity extends AppCompatActivity {
  @Override
  protected void onStart() {
    super.onStart();
    Uri mLink = getIntent().getData();
    if (mLink != null) {
        MagicWindowSDK.getMLink().router(mLink);
    } else {
        MLink.getInstance(this).checkYYB();
    }
  }
}
```
情况②，启动页的启动方式为singleTop模式时，需要在其Activity内覆写onNewIntent()
```
    @Override
    public void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        this.setIntent(intent);
        
        Uri mLink = intent.getData();
        if (mLink != null) {
            MagicWindowSDK.getMLink().router(mLink);
        } else {
            MLink.getInstance(this).checkYYB();
        }
   }
```

###Qe4.如何开启应用宝跳转。
A:<br>
1. 在魔窗后台，进入“mLink”菜单下“高级设置”，填写应用宝微下载地址。如何配置微下载地址：
登录腾讯开放平台，选择一款Android应用，选择“运营服务”中的“微下载”获取微下载地址，格式为“http://a.app.qq.com/o/simple.jsp?pkgname=包名”， 如http://a.app.qq.com/o/simple.jsp?pkgname=me.bolo.android.client 为波罗蜜的微下载地址。<br>
2. 配置完上述内容后，在“高级设置”中开启“Android开启应用宝跳转”的开关。<br>
3. 代码集成，要在代码的相应位置需要调用checkYYB（在耗时的启动之后，跟启动首页的startActivity并列放置）
http://www.magicwindow.cn/doc/sdk-android.html#begin-start/mLink 中章节5.2.5
4.应用宝上的包名与版本号要与App的保持一致。否则应用宝无法打开App。

###Qe5.开启了应用宝跳转，为何getIntent().getData()为空
A:应用宝打开App是通过包名来直接打开App的，而不是scheme，所以getData()为空。<br>
也正因如此，所以我们需要在getData()为空时调用checkYYB()接口，来通过后台拿到具体页面的Scheme。从而跳转到具体页面<br>

###Qe6.应用打开具体页面后，又重新打开了首页。
A:此问题分两种情况<br>
①未开启应用宝时， 说明router调用时，有一个handler之类的延迟操作重新打开了首页。走router时需要将handler排除掉。<br>
②开启应用宝时，说明checkYYB接口调用前后有比较耗时的初始化操作，将checkYYB移到耗时操作之后，跟进入首页的startActivity并列放置即可。

###Qe7.checkYYB()是如何实现的？为何YYBCallback只有一个onFailed()？
A:
如果用户在微信中使用短链接，建议App在我们后台开启应用宝。<br>
因为从微信过来的是拿不到getIntent().getData()的，无法调用router()。
所以需要使用checkYYB()。而checkYYB()是一个模糊匹配的过程，它会调用网络的接口，然后在sdk内部进行匹配，如果匹配上了它本身会调用类似router()，所以无需成功的回调。
checkYYB()方法建议使用带YYBCallback的，因为checkYYB()本身有超时机制，超过一定的时间或者没匹配上就会走onFailed()。
onFailed()可以是跳到App的MainActivity之类的。

其他
===
经过以上分析，我们总结一下常见错误以及注意点：<br>
①	基础配置不要写错，比如Session和AndroidManifest.xml内的APP_ID。<br>
②	register不要写错。（注解方式和自定义方式二选一）<br>
③	router不要漏写，且注意书写的位置。如果有开机动画，或者handler之类，要根据实际情况填写。<br>
④	如果开启了应用宝，注意checkYYB接口不要遗漏，注意一定要写在耗时的初始化之后，跟进入首页的startActivity放在一起。<br>
⑤	混淆时注意将魔窗keep出来。<br>
⑥	后台填写Scheme和配置mLink服务需要注意，其中Scheme需要用全小写字母。如果有数字，数字不能放在开头。<br>
