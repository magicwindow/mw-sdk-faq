#魔窗iOS SDK FAQ
1.配置好的短链接为何无法从微信跳转到app？
* 确认设备是否是iOS9以上
* 短链接所在的mLink基础配置是否使用了universal link 服务
* app中是否进行了相关universal link的配置
* 确认相关参数是否配置正确

2.短链接所在的mLink基础配置和app都配置了universal link，但是无法从微信跳转到app？
* 确认设备是否是iOS9以上
* 在微信中按照提示，在safari中打开，下拉页面，查看上面是否有打开按钮
如果有打开按钮，点击打开就可以跳转到app，然后再次从微信中点击短链接即可从微信中直接跳转到app
如果没有打开按钮，请确认下面的内容都是填写正确 （applinks:s.mlinks.cc）
![](images/ios-1.jpg)