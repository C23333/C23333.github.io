# C++成员函数与友元函数重载运算符

  ## 1.C++中不可重载的运算符

> C++中不能重载的运算符有5个，分别为: **?    .      ::     sizeof      .* **

> 对双目运算符而言，成员函数重载运算符的函数参数表中只有一个参数，而用友元函数重载运算符函数参数表中含有两个参数。
>  对单木运算符来说，成员函数重载运算符的函数参数表中没有参数，而用友元函数重载运算符函数参数表中含有一个函数。这个问题要搞清楚，有一个this指针的问题

> 双目运算符一般可以用友元函数重载和成员函数重载，但有一种情况只可以用友元函数重载。
>   即：双目运算符左边的变量是一个常量，而不是对象！！！



## 2.一般经验

* 对于单目运算符，建议选择成员函数；
* **对于运算符“=，（），[]，->”只能作为成员函数；**
* 对于运算符“+ =，-=，/=，*=，&=，！=，~=，%=，<<=，>>=”建议重载为成员函数；
* 对于其他运算符，建议重载为友元函数。



## 3.class中将operator函数定义为friend主要有以下考虑:

* friend function是对外公开的，而class method是属于对象的，有些情况调用不方便 
* 对某些需要两个参数的operator function，定义friend比较方便，如下例中operator << 
* 所有class  method必须有匹配的左值类型进行调用而friend则无需这样，只要能隐式转化成当前类型就可以调用该函数，因此如下例构造函数没有定义为explicit的，可以进行隐式转化，就可以在不同类型间运算。  
* *下面的例子可以很好的说明定义为friend  function的好处.*

~~~c++
#include   <iostream.h>   
  class   point   
  {   
          int   x;   
          int   y;   
          public:   
          point(int   vx=0){x=vx;y=0;}   
          point(int   vx,int   vy):x(vx),y(vy){}   
          friend   point   operator   +(point   p1,point   p2);   
          friend   ostream   &   operator   <<(ostream   &output,point   &p1);   
  };   
    
  point   operator   +(point   p1,point   p2)   
  {   
          point   p;   
          p.x=p1.x+p2.x;   
          p.y=p1.y+p2.y;   
          return   p;   
  }   
    
    
  ostream   &   operator   <<(ostream   &output,point   &p1)   
  {   
          output<<p1.x<<'+'<<p1.y<<'i'<<endl;   
          return   output;   
  }   
    
  int   main()   
  {   
        point   p1(1,2),p2(5,6);   
        point   p3,p4;   
        p3=p1+p2;   
        p4=1+p2;//如果定义为class   method,编译将出错! 即使类提供了相应的构造函数，且非explicit     
        cout<<p1<<p2;   
        cout<<p3<<p4;   
        return     0;   
  }   
~~~



## 对于流插入和流提取运算符来说，为什么只能重载为友元函数

**因为”位置“**

  对于流插入和流提取运算符来说，我们常用的写法是

~~~c++
cout << sth;
~~~

 我们知道成员函数有一个隐藏的参数this，它在我们自己定义的参数之前，那么如果我们真的使用成员函数重载，

```c++
ostream & operator <<(ostream & cout);
```

实际上是

~~~c++
ostream & operator <<(myclass * this， ostream & cout);
~~~

使用的时候是

~~~c++
sth << cout;
~~~

**这既不符合我们的使用习惯，**
**也不能使用形如**

~~~c++
cout << myclass1 << myclass2 ;
~~~

**这样的链式输出。**

​    对于=、()、[]、->这样只能使用成员函数重载的运算符也一样，只有使用成员函数重载才能防止出现如

~~~c++
1 = x， 1[x],   1->x,  1(x)
~~~

这样的合法语句。