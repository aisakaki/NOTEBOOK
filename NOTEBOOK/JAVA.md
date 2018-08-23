JAVA
====
1. java a.class
找不到或无法加载主类

正解：java a
不要加后缀

2. mom a = new son();
当向上转型后，子类的方法必须在基类有声明


3. 对于 mom object = new son()的理解
先new son()新建一个对象
然后mom object声明一个引用
构造器执行顺序为mom然后son
这两个构造器的执行都是在new son()的时候执行的

—>构造器继承问题。
在加载类的时候，会依次访问加载基类（或叫父类，超类），其中在遇到static对象和代码段的时候，会依程序中的顺序初始化。（而构造器也是static方法，只是没有写出来而已。
所以举例 对继承关系 A->B->C->D
在生成D类对象的时候，会执行构造器且构造器执行顺序为A->B->C->D