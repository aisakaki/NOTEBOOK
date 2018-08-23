android遇到的坑
====
1. 界面成员初始化不能放在成员变量进行
比如这样就会出现运行时错误！
```
public class MainActivity extends AppCompatActivity {
    private EditText editText = findViewById(R.id.t1);
    private EditText editText2 = findViewById(R.id.t2);
    private  Button button = findViewById(R.id.b);
    private TextView textView = findViewById(R.id.show);
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        button.setOnClickListener(new listener());

    }
}
```
这样会导致初始化失败。（具体机理需要更深入了解）
应对放在onCreate中进行初始化，可以在成员变量中定义之以供全局使用
```
public class MainActivity extends AppCompatActivity {
    private EditText editText;
    private EditText editText2;
    private  Button button;
    private TextView textView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = findViewById(R.id.t1);
        editText2 = findViewById(R.id.t2);
        button  = findViewById(R.id.b);
        textView = findViewById(R.id.show);
        button.setOnClickListener(new listener());

    }
}
```

2. 手动编辑xml界面
the view is not constrained it only has designtime positions...
https://blog.csdn.net/BSSYNHDJZMH/article/details/79728625

3. AS3.0 PROVIDED已被更换为compile only
https://juejin.im/entry/59f9785851882515d254a375

4. gradel的依赖中 要把1.1.1改为
implementation 'com.android.support.constraint:constraint-layout:1.1.0' 
1.1.1版本下载不下来

5. 列表视图切换project则可以显示project目录视图（实际的文件储存），
package视图为 逻辑视图 （在文件储存不考虑后实际开发的时候用）
Android视图开发也常用

6. 自己设置入口的话，需要在assets下新建一个入口的指示文件
并且要将run configuration里的lanuch option改为nothing

7. `<meta-data/>`标签是放在application标签里的！
比如
```
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
    <meta-data
        android:name="xposedmodule"
        android:value="true" />

    <!-- 模块描述，显示在xposed模块列表那里第二行 -->
    <meta-data
        android:name="xposeddescription"
        android:value="test" />

    <!-- 最低xposed版本号(lib文件名可知) -->
    <meta-data
        android:name="xposedminversion"
        android:value="54" />
    </application>
    <!-- 是否是xposed模块，xposed根据这个来判断是否是模块 -->
```

8. 在编辑区域按下快捷键 ctrl + o ，弹出可重写的所有方法列表

9. 环境变量到` ~.bashrc`里去配置 (记得source） `/etc/profile`里会失效

