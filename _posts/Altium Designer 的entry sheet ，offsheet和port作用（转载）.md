## [Altium Designer 的entry sheet ，offsheet和port作用（转载）](https://www.cnblogs.com/liuck/p/3988898.html)

1. **图纸结构**

图纸包括两种结构关系： 一种是层次式图纸，该连接关系是纵向的，也就是某一层次的图纸只能和相邻的上级或下级有关系。

另一种是扁平式图纸，该连接关系是横向的，任何两张图纸之间都可以建立信号连接。

2. **网络连接方式**

   

　Altium Designer提供了6类网络标识分别是：Net Label(网络标号)，Port(端口)，Sheet Entry(图纸入口)，　

​                              Power Port(电源端口)，Hidden Pin(隐匿引脚)、Off-sheet Connector(图纸外连接符)。

​                              网络标识是通过名字来连接的，名字相同就可以传递信号。但是特别要注意的是，除了“Port”与“Sheet Entry”这一对标识以外，

​                              其它不同类的网络标识，即使标识名字相同，相互之间也没有连接。比如Net Label及Port两种标识，只能通过连线才能把这两   

​                             个同 名 不同类的标识连接起来。“Automatic”是缺省选项，表示系统会检测项目图纸内容，从而自动调整网络标识的范围。

​                             检测及自动调整的过程如下：如果原理图里有Sheet Entry标识，则网络标识的范围调整为Hierarchical。

3. **“Port”及“Net Label”的作用范围 **

这两种网络标识的作用范围是可以变化和更改的。方法是：打开Project＼Project Option＼Option标签，在Net  Identifier Scope一栏的四个选项(Automatic、Flat、Hierarchical、Global)中挑一项。
   “Automatic”是缺省选项，表示系统会检测项目图纸内容，从而自动调整网络标识的范围。检测及自动调整的过程如下：如果原理图里有Sheet Entry标识，则网络标识的范围调整为Hierarchical。

​    如果原理 图里没有Sheet Entry标识。但是有Port标识，则网络标识的范围调整为Flat。如果原理图里既没有Sheet Entry标识，又没有Port标识，则Net Label的范围调整为Global。
   “Flat”代表扁平式图纸结构，这种情况下，Net Label的作用范围仍是单张图纸以内。而Port的作用范围扩大到所有图纸，各图纸只要有相同的Port名，就可以发生信号传递。
   “Hierarchical”代表层次式结构，这种情况下，Net Label，Port的作用范围是单张图纸以内。当然，Port可以与上层的Sheet Entry连接，以纵向方式在图纸之间传递信号。
   “Global”是最开放的连接方式，这种情况下，Net Label、Port的作用范围都扩大到所有图纸。各图纸只要有相同的Port或相同的Net Label，就可以发生信号传递。

