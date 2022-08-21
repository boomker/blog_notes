# Python全栈之路系列之面向对象Python2.7类继承

## 继承类定义

单继承

```Python
class <类名>(父类名)
  <代码快>
```

类的多重继承

```Python
class 类名(父类1,父类2,....,父类n)
   <代码块>
```

1. Python的类可以继承多个类，Java和C#中则只能继承一个类
2. Python的类如果继承了多个类，那么其寻找方法的方式有两种，分别是：深度优先和广度优先

![class-inheritance-01](../images/2016/12/1483021455.png)

- 当类是经典类时，多继承情况下，会按照深度优先方式查找
- 当类是新式类时，多继承情况下，会按照广度优先方式查找

经典类和新式类，从字面上可以看出一个老一个新，新的必然包含了跟多的功能，也是之后推荐的写法，从写法上区分的话，如果 当前类或者父类继承了object类，那么该类便是新式类，否则便是经典类。

![class-inheritance-02](../images/2016/12/1483021475.png)
![class-inheritance-03](../images/2016/12/1483021498.png)

经典类多继承

```Python
class D:

    def bar(self):
        print 'D.bar'

class C(D):

    def bar(self):
        print 'C.bar'

class B(D):

    def bar(self):
        print 'B.bar'

class A(B, C):

    def bar(self):
        print 'A.bar'

a = A()
# 执行bar方法时
# 首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去D类中找，如果D类中么有，则继续去C类中找，如果还是未找到，则报错
# 所以，查找顺序：A --> B --> D --> C
# 在上述查找bar方法的过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
a.bar()
```

新式类多继承

```Python
class D(object):

    def bar(self):
        print 'D.bar'

class C(D):

    def bar(self):
        print 'C.bar'

class B(D):

    def bar(self):
        print 'B.bar'

class A(B, C):

    def bar(self):
        print 'A.bar'

a = A()
# 执行bar方法时
# 首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去C类中找，如果C类中么有，则继续去D类中找，如果还是未找到，则报错
# 所以，查找顺序：A --> B --> C --> D
# 在上述查找bar方法的过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
a.bar()
```

1. 经典类：首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去D类中找，如果D类中么有，则继续去C类中找，如果还是未找到，则报错
2. 新式类：首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去C类中找，如果C类中么有，则继续去D类中找，如果还是未找到，则报错

**注意：88在上述查找过程中，一旦找到，则寻找过程立即中断，便不会再继续找