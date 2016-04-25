#魔窗Android SDK FAQ
###Q1.魔窗位活动不能展示
A：如果出现此问题，请按照以下步骤排查。<br>
1.AndroidManifest.xml内正确填写魔窗APP_ID以及MWActivity等。<br>
2.魔窗后台正确开启活动，并投放到相应的魔窗位<br>
3.确保代码中MWImageView绑定正确的魔窗位key<br>
4.正确集成Session

###Q2.Jar包重复（暂未集成新浪微博分享，忽略此步）
A:如果显示微信分享等jar包重复。（特别如果集成了shareSDK，会报微博的jar包重复）<br>
1.将微信jar包libmma.jar删除。<br>
2.初始化魔窗SDK时，启用shareSDK<br>
```Java
  MWConfiguration config = new MWConfiguration(this);
  config.setSharePlatform(MWConfiguration.SHARE_SDK);
```
###Q3.分享弹框缺少图标
A:将assets文件夹下的文件拷贝到相应文件夹即可。
