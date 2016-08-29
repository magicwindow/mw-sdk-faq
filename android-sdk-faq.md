#魔窗Android SDK FAQ

FAQ分类
---

* [魔窗位](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#魔窗位)
* [分享](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#分享)
* [短链接](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#短链接)
* [mLink](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#mLink)
* [应用宝](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#应用宝)
* [其他](https://github.com/magicwindow/mw-sdk-faq/blob/master/android-sdk-faq.md#其他)

魔窗位
===

###Q1.魔窗位活动不能展示
A:如果出现此问题，请按照以下步骤排查。<br>
1. AndroidManifest.xml内正确填写魔窗APP_ID以及MWActivity等。<br>
2. 魔窗后台正确开启活动，并投放到相应的魔窗位<br>
3. 确保代码中MWImageView绑定正确的魔窗位key<br>
4. 正确集成Session

分享
===
###Q2.分享弹框缺少图标
A:将assets文件夹下的文件拷贝到相应文件夹即可。

###Q3.不能分享
A:现阶段魔窗支持微信分享。<br>
1）前往微信开放平台申请微信AppID，
[参考文档](https://open.weixin.qq.com)<br>
注意：包名以及应用签名不要写错。<br>
2）将微信AppID填写在魔窗后台->帐号设置->应用管理->设置分享平台<br>

###Q4.Jar包重复（暂未集成新浪微博分享，忽略此步）
A:如果显示微信分享等jar包重复。（特别如果集成了shareSDK，会报微博的jar包重复）<br>
1. 将微信jar包libmma.jar删除。<br>
2. 初始化魔窗SDK时，启用shareSDK<br>
```Java
  MWConfiguration config = new MWConfiguration(this);
  config.setSharePlatform(MWConfiguration.SHARE_SDK);
```

短链接
====
###Q5.短链内的参数值能动态修改么？
A:可以，短链支持将参数的动态值作为 query 放在后面<br>
例如:http://a.t.mlinks.cc/ANax?id=12345

###Q6.App安装的前提下，短链依旧前往下载App页面。
A:换一台其他品牌的手机验证或者换一个浏览器进行验证比如UC、QQ浏览器，因为有些机型原生浏览器不支持scheme。如果依旧不行，则排查是否Scheme配置错误。<br>
遇到这个问题的时候，我们的第一反应是检查Scheme有没有配置错误。因为程序未能打开App，所以肯定是集成的初始阶段出了问题。<br>
①检查AndroidManifest.xml内的Scheme是否配置正确。此处比较容易出错的是，后台配置scheme需要”://”，而Android内配置时不需要”://”<br>
②后台填写Scheme和配置mLink服务需要注意，其中Scheme需要用全小写字母。如果有数字，数字不能放在开头。<br>

###Q7.App安装的前提下，短链只能打开首页，未能进入具体页面。
A:有如下可能原因。 <br>
①	检查register回调有没有写错。（register要写在启动页的onCreate内）<br>
②	检查router入口有没有漏掉或者写错位置。 （router是真正的入口，如果有启动动画等，可以方在动画后。）<br>
③	程序做了混淆，但是没有将魔窗的keep出来。<br>
④	如果开启了应用宝跳转，则检查checkYYB接口有没有漏写，（checkYYB需要放在耗时的启动之后，跟启动首页的startActivity放在一起。）<br>
如果以上都没有错误，我们可以利用Charles抓包，查看dpls/v2接口内有没有正确返回mLink的服务uri。从而判断后台是否配置正确。

###Q8.通过短链进入具体页面后，清除数据再次打开App，依旧进入短链对应的具体页面。
A:“程序安装后第一次打开，魔窗mLink会跟后台通信实现场景还原。App清除数据后，mLink会判断程序为第一次安装。此时请求后台并匹配成功。所以会进入具体页面”。<br>
用户实际使用时基本不会发生此类情况。属于极小概率事件。<br>

mLink
===
###Q9.App未安装时，通过短链跳转到下载页面，安装后，第一次打开未能实现场景还原。
A:router必须要写在启动页。否则安装后，第一次打开无法场景还原，且走了router后就不要再走跳转到首页的逻辑。<br>

###Q10.通过mLink跳转直达的页面，如何做到“先显示启动动画，然后再做相应跳转”
A:可以在动画结束时再调用router,如下：<br>
```Java
Uri mLink = getIntent().getData();
            if(mLink!=null){
                MLink.getInstance(this).router(mLink);
            }            
```
###Q11.通过mLink跳转直达的页面，如何做到“返回时进入首页，而不是退出程序”
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

###Q12.可以跳转到具体页面，但是不知道如何拿到参数。
A：如果利用自定义注解，则所有的参数，都放在MLinkCallback回调的Map<String, String> paramMap内。<br>
如果利用注解，则可用getIntent().getStringExtra("key")获取。


###Q13.提示MLink内的defaultMLinkCallback持有activity导致内存泄露。
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


###Q14.mLink集成跳转到相应activity时黑屏
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


应用宝
===
###Q15.集成了应用宝跳转，但是从微信打开时依旧会显示中间页,没有直接打开App。
A：说明后台没有打开应用宝开关。需要在魔窗后台将下载链接改为应用宝的地址，并且在mLink服务的高级设置内打开应用宝跳转开关。

###Q16.程序在后台时，从微信内通过应用宝跳转无法跳转到具体页。
A:
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
###Q17.如何开启应用宝跳转
A:<br>
1、魔窗后台，先进入“App管理”页面修改Android的App默认下载链接为应用宝下载链接。<br>
2、魔窗后台，进入高级设置中打开应用宝跳转开关。<br>
3、代码集成，要在代码的相应位置需要调用checkYYB（在耗时的启动之后，跟启动首页的startActivity并列放置）<br>

###Q18.开启了应用宝跳转，为何getIntent().getData()为空
A:应用宝打开App是通过包名来直接打开App的，而不是scheme，所以getData()为空。<br>
也正因如此，所以我们需要在getData()为空时调用checkYYB()接口，来通过后台拿到具体页面的Scheme。从而跳转到具体页面<br>

###Q19.应用打开具体页面后，又重新打开了首页。
A:此问题分两种情况<br>
①未开启应用宝时， 说明router调用时，有一个handler之类的延迟操作重新打开了首页。走router时需要将handler排除掉。<br>
②开启应用宝时，说明checkYYB接口调用前后有比较耗时的初始化操作，将checkYYB移到耗时操作之后，跟进入首页的startActivity并列放置即可。


其他
===
经过以上分析，我们总结一下常见错误以及注意点：<br>
①	基础配置不要写错，比如Session和AndroidManifest.xml内的APP_ID。<br>
②	register不要写错。（注解方式和自定义方式二选一）<br>
③	router不要漏写，且注意书写的位置。如果有开机动画，或者handler之类，要根据实际情况填写。<br>
④	如果开启了应用宝，注意checkYYB接口不要遗漏，注意一定要写在耗时的初始化之后，跟进入首页的startActivity放在一起。<br>
⑤	混淆时注意将魔窗keep出来。<br>
⑥	后台填写Scheme和配置mLink服务需要注意，其中Scheme需要用全小写字母。如果有数字，数字不能放在开头。<br>
