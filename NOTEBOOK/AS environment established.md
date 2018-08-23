AS环境搭建
====
1. 由于谷歌被墙，所以国内很不方便
- 方法一，直连谷歌：改hosts（**最好的办法**，可以解决sdk和gradel安装）
dl.google.com  203.208.40.70
dl-ssl.google.com 74.125.23.190
并注意关掉gradel代理

- 方法二，离线安装。
 - SDK:离线安装SDK，并修改SDK路径
 - GRADEL:手动离线安装gradel，将gradel压缩包放入wrapper里的乱码文件夹里，放一个压缩包和一个文件夹
然后在setting里手动设置gradel路径到4.4-all

2. 无限卡下载SDK
在/home/aisaka/android-studio/bin/idea.properties中加入一行
disable.android.first.run=true

3. 运行模拟器时无法加载驱动问题
```
 failed to load driver: nouveau
下午3:55	Emulator: libGL error: unable to load driver: nouveau_dri.so

下午3:55	Emulator: libGL error: driver pointer missing

下午3:55	Emulator: libGL error: failed to load driver: nouveau

下午3:55	Emulator: libGL error: unable to load driver: swrast_dri.so

下午3:55	Emulator: libGL error: failed to load driver: swrast

下午3:55	Emulator: emulator: ERROR: Missing initial data partition file: /home/aisaka/.android/avd/Nexus_5X_API_27.avd/userdata.img

下午3:55	Emulator: X Error of failed request:  BadValue (integer parameter out of range for operation)

下午3:55	Emulator: Major opcode of failed request:  155 (GLX)

下午3:55	Emulator: Minor opcode of failed request:  24 (X_GLXCreateNewContext)

下午3:55	Emulator: Value in failed request:  0x0

下午3:55	Emulator: Serial number of failed request:  58

下午3:55	Emulator: Current serial number in output stream:  59

下午3:55	Emulator: Process finished with exit code 1

下午3:55	Gradle build finished in 1s 316ms
```
问题解决：https://kotlintc.com/articles/4062
执行如下命令：
```
$ cd ~/Android/Sdk/emulator/lib64/libstdc++

$ mv libstdc++.so.6 libstdc++.so.6.bak

$ ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so.6 libstdc++.so.6
```
4. 我目前的gradel（4.4）对应的版本是这样的。有些版本的依赖包下载不下来。应该是gradel版本太低问题，根本解决办法为下载最新的gradel
```
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'
    implementation 'com.android.support.constraint:constraint-layout:1.1.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}
```
5. 在gradel-wrapper.properties中的distrubutionUrl中修改版本 然后点击sync now，即可升级gradel
前面的包缺失，大部分都是因为gradel版本太低的问题
