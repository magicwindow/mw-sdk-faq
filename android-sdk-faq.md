#魔窗Android SDK FAQ
###Q1.魔窗位活动不能展示
A:如果出现此问题，请按照以下步骤排查。<br>
1. AndroidManifest.xml内正确填写魔窗APP_ID以及MWActivity等。<br>
2. 魔窗后台正确开启活动，并投放到相应的魔窗位<br>
3. 确保代码中MWImageView绑定正确的魔窗位key<br>
4. 正确集成Session

###Q2.Jar包重复（暂未集成新浪微博分享，忽略此步）
A:如果显示微信分享等jar包重复。（特别如果集成了shareSDK，会报微博的jar包重复）<br>
1. 将微信jar包libmma.jar删除。<br>
2. 初始化魔窗SDK时，启用shareSDK<br>
```Java
  MWConfiguration config = new MWConfiguration(this);
  config.setSharePlatform(MWConfiguration.SHARE_SDK);
```
###Q3.分享弹框缺少图标
A:将assets文件夹下的文件拷贝到相应文件夹即可。

###Q4.不能分享
A:现阶段魔窗支持微信分享。<br>
1）前往微信开放平台申请微信AppID，
[参考文档](https://open.weixin.qq.com)<br>
注意：包名以及应用签名不要写错。<br>
2）将微信AppID填写在魔窗后台->帐号设置->应用管理->设置分享平台<br>

###Q5.mLink集成跳转到相应activity时黑屏
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
###Q6.通过mLink跳转直达的页面，如何做到“先显示启动动画，然后再做相应跳转”
A:可以在动画结束时再调用router,如下：<br>
```Java
Uri mLink = getIntent().getData();
            if(mLink!=null){
                MLink.getInstance(this).router(mLink);
            }            
```
###Q7.通过mLink跳转直达的页面，如何做到“返回时进入首页，而不是退出程序”
A:可以处理直达页面的返回函数。<br>
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
                this.startActivity(intent);

            }
        });        
```
第二步：在DetailActivity内的返回函数内，根据intent.getBooleanExtra("mlink",false)是否为true来跳转到首页。

###Q8.短链内的参数值能动态修改么？
A:可以，短链支持将参数的动态值作为 query 放在后面<br>
例如:http://a.t.mlinks.cc/ANax?id=12345
###Q9.App安装的前提下，短链依旧前往下载App页面。
A:换一台其他品牌的手机验证，因为有些机型原生浏览器不支持scheme。如果依旧不行，则排查是否Scheme配置错误。<br>
###Q10.App安装的前提下，短链只能打开首页，未能进入具体页面。
A:有如下可能原因。
①检查register回调有没有写错。
②检查router入口有没有漏掉或者写错位置。
③如果开启了应用宝跳转，则检查checkYYB接口有没有漏写
###Q11.通过短链进入具体页面后，清除数据再次打开App，依旧进入短链对应的具体页面。
A:“程序安装后第一次打开，魔窗mLink会跟后台通信实现场景还原。App清除数据后，mLink会判断程序为第一次安装。此时请求后台并匹配成功。所以会进入具体页面”。用户实际使用时基本不会发生此类情况。属于极小概率事件。