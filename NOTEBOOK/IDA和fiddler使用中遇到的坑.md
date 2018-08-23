IDA和fiddler使用中遇到的坑
====
IDA
1. IDA无法用F5将汇编语言转换为伪代码可能是因为用了64位版本IDA去分析32位APK

2. 
- 无权限修改android目录问题
需要使用
mount -o rw,remount -t yaffs2 /dev/block/mtdblock3 /system
重新挂载（这个语句重新挂载了/system目录）
如需要修改其他目录亦需要执行对应语句

然后再chmod修改相应目录（本语句对应/system）权限
- 在权限正确的情况下。设备上的文件路径普通权限可能无法直接写入，如果你的设备已经 root 过，可以先 adb push /path/on/pc /sdcard/filename，然后 adb shell 和 su 在 adb shell 里获取 root 权限后，cp /sdcard/filename /path/on/device


3. 随时注意权限问题

4. android server必须以root权限运行

5. 彻底解决INSTALL_FAILED_UPDATE_INCOMPATIBLE的安装错误、安装包与之前设备上的安装包签名不一致

6. 签名问题

7.
- 通过jdb调试的时候，需要先指定双方（电脑和手机）的调试端口！
先adb jdwp查看欲用jdb调试的进程的PID（jdwp即为手机端负责jdb调试的服务器）
adb forward tcp:8000 jdwp:xxx  
然后
jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8000
or 
jdb -attach localhost:8700
-通过IDA调试的时候，也需要先指定双方的调试端口
android_server即为手机端的服务器，先运行之（root权限）
然后
adb forward tcp:23946 tcp:23946
再在IDA里设置本地端debugger
注意设置debugger option（三个suspend）
而且 debugger option必须在attach成功后在设置 （而不是在attach面板设置！）
debugger option那三个意思是使得程序在程序加载，线程加载，库加载的时候暂停
当要和手机端连接调试的时候，肯定都是以C/S模式的，所以需要用adb forward指定双方端口才行


8. ps|grep xxx

9. android:debuggable标签要写在application里！
且android manifest的一些选项错误在运行时不报错
======深入了解android manifest应该怎么写

10. adb debug启动配合jdb和ida进行调试是因为有可能反调试代码存在于so库加载之前
所以我们必须在加载so库之前的JNI_ONLOAD和init_array的时候调试，这只能用debug启动，不能用常规调试下断点，因为常规调试下断点的时候，so库就已经加载完成了
所以我们通过debug启动之后，在so库加载完之前，到JNI_ONLOAD和init_array中设断点
jdb和ida都调试的是同一个程序，但他们的调试的进程/线程不一样。但是他们都是调试的同一个程序。

11. IDA在调试的时候 需要单步调试
F7(step into),F8(step over)都是单步调试
存在于debugger菜单里
step into：单步执行，遇到子函数就进入并且继续单步执行（简而言之，进入子函数）；
step over：在单步执行时，在函数内遇到子函数时不会进入子函数内单步执行，而是将子函数整个执行完再停止，也就是把子函数整个作为一步。有一点,经过我们简单的调试,在不存在子函数的情况下是和step into效果一样的（简而言之，越过子函数，但子函数会执行）。
step out：当单步执行到子函数内时，用step out就可以执行完子函数余下部分，并返回到上一层函数

12. test段存着代码部分。
搞清楚自己要找的是哪个函数 找到text中的相对地址
左边有functions window显示了程序的所有函数！

13. 观察和识别 在单步调试的时候 ，某个函数退出的现象。见黄皮书P352 JNI_ONLOAD退出的现象。原本单步调试正常，每次进行到BLX R7这步的时候，程序就跳转到另一个地方去了（libc.so)，没法继续单步从刚刚的地方进行下去=>即该函数已经退出了(JNI_ONLOAD退出）

14. IDA需要动静结合调试
之所以需要动态调试，是因为很多时候程序中的储存器或答案是会在运行中被修改的，他不是固定的静态储存在一个地方，我们需要观测跟踪。

15. IDA调试的时候，光标到达某一行的时候，该行还没开始运行，下一步才开始运行

16. 黄皮书P353
静态分析的时候，由于没有进行调试，所以反调试指令不会运行，故所有程序都会被加载到内存并翻译成字节码，所以可以在Hex view中观测到。
善用动静结合分析。反调试代码段的内存Hex不能再动态分析中观察到，因为在动态分析时，调试到BLX R7这步时，内存还未加载这个指令，还没有把这条指令翻译成字节码，故Hex中当然为00000000，一旦加载这个指令，JNI_ONLOAD就退出了（因为反调试代码检测到有外部程序在本程序中下断点）！所以只能查看静态分析中的.text段中的JNI_onload

17. 在逆向so库，需要修改so库的时候 
动态分析的时候IDA加载的是【整个apk程序】，而静态分析的时候IDA记载的是【so库】（自己指定加载什么）
由静态得到函数相对so于so库的地址，由动态分析得到so库加载在内存的绝对地址，进而得到函数加载在内存的绝对地址，跳到地址处，进行动态调试。调试得到结果，确定要修改的语句之后，返回静态IDA，查看该语句在so库中的Hex相对地址。
然后打开010editor，打开so库，编辑该地址，回编译，签名，即可生成修改后的apk。

18. 签名的时候
java -jar signapk.jar testkey.x509.pem testkey.pk8 d3.apk final.apk
testkey.x509.pem testkey.pk8顺序不能变。（不知道为什么，待查）

19. 思路清晰 现在要调试谁 修改谁


---
Fiddler
1. mono打开fiddler出错
必须要指定32位 如下语句
mono --arch=32 Fiddler.exe

2. android手机端无法设置电脑为代理问题
①fiddler设置好后要重新启动fiddler！
②要进入http://proxy_ip:proxy_port 下载安装证书
