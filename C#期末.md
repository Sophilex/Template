## C#字符串是不可变的

## 面向对象的最基本的特征是抽象性、封装性、继承性和多态性

 ![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

 ![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

##  模态对话框vs非模态对话框

![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)

模态对话框关闭后会返回一个cancle结果

非模态对话框会返回none（没信息）

$\textcolor{blue}{messagebox就是一个模态对话框}$

#### messagebox的应用：

text：文本

button：按钮选项

icon：图标

**$\textcolor{blue}{eg:}$**

`MessageBox.Show("hello","wow",MessageBoxButtons.YesNo,MessageBoxIcon.Question);`

![image-20221220091259778](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220091259778.png)

## 静态：

静态方法：只有用类名才能调用，实例个体不能调用

静态成员不能访问实例成员，但是实例成员可以访问静态成员

![image-20221220103328808](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220103328808.png)

![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image010.png)

#### 个人认为可以这么理解：

静态是比实例要低一级的存在，所以在一个类里面，实例成员可以调用静态成员‘，但是静态成员不能调用实例成员。但是一旦这个类实例化了，静态方法是大家的但不是它的，所以实例对象不能调用静态成员。

静态方法属于类本身,当你把这个类实例化了出来,但是静态方法并不属于实例化的个体(这个是公有财产,大家都可以用类去调用它,但是它是集体的,不属于任何个人)

 

### 静态类：只包含静态成员，不能包含实例成员

![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image012.png)

一旦我将类设置为静态类，里面的实例成员就是非法的了

![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image014.png)

会发现**$\textcolor{red}{静态类也无法被实例化}$**

 

## 类的继承：

#### $\textcolor{red}{这里不允许多重类继承，一个类最多只能继承一个父类，但是，是可以多代继承的，比如A继承于B，B又继承于C}$

B继承A：  A（父类，基类），B（子类，派生类）

![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image016.png)

##### protected修饰符

该成员可以在子类中访问

不能在本类和子类外的其它地方访问

 

## 多态的实现：

### 1．方法的隐藏

 ![img](file:///C:/Users/26463/AppData/Local/Temp/msohtmlclip1/01/clip_image018.png)

### 2． 重写虚方法

虚方法重写的时候，其需要的参数，返回的类型都需要和父类中对应的虚方法相一致，因为它其实还是在调用基类的方法。但是隐藏父类方法时可以改变所需的参数，也可以改变返回的类型，因为它调用的是派生类的方法。

另外隐藏父类方法需要写上new，而父类虚方法重写需要在父类方法处加上virtual，在子类方法处加上override.

◦   **$\textcolor{red}{ATTETION}$**：

（1）使用virtual修饰符后，不允许再使用static、abstract或override修饰符；

（2）派生类对象即使被强制转换为基类对象，所引用的仍然是派生类的成员；

（3）override的方法仍然是虚方法；

当派生类替换或重写基类方法后，如果想调用基类的同名方法，可以使用base关键字。如：base.eat();

![image-20221220000313891](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220000313891.png)

 

### 3.抽象方法与虚方法：
（1）抽象方法：只有方法名称，没有方法体（也就是没有具体实现），子类必须重写父类的抽象方法

​          虚函数：该方法由方法体，但是子类可以覆盖，也可以不覆盖

（2）虚方法有方法体，抽象方法没有方法体。抽象方法是一种**强制派生类覆盖的方法**（除非派生类也是一个抽象类），		  否则派生类将不能被实例化**$\textcolor{green}{but抽象类的派生类不用重写抽象类的实例方法，只用重写抽象方法）}$**

（3）**抽象方法只能在抽象类中声明**，虚方法不是

（4）派生类必须重写抽象类中的抽象方法，虚方法则不必要（虚方法也可以不写）

（5）子类重写抽象方法/虚方法时都需要加上override，两类方法的声明也都要加上virtual



### 4.接口

![image-20221219234741908](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221219234741908.png)

![image-20221220105325001](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220105325001.png)

**$\textcolor{red}{接口里面可以有方法，也可以有属性，但是不能有实例字段，但是可以有静态字段}$**

（1）接口里的方法不能有修饰符，默认是public（private 和protected 不行）

（2）接口的方法不能有函数体（$\textcolor{red}{这里跟抽象类一样}$）

（3）接口中不能包含实例字段，不能有构造函数

（4）接口里的方法被继承的类重写时，不需要用override关键字，接口不能被实例化

（5）接口之间可以继承，并且可以多继承；接口不能继承于类，但是类可以继承于接口

（6）一个类可以同时继承一个类，并实现多个接口

（7）一个类必须要实现接口的所有函数

### 5.抽象类VS接口

1.飞机会飞，鸟会飞，他们都继承了同一个接口“飞”；但是飞机属于飞机抽象类，鸽子属于鸟抽象类。

2.就像铁门木门都是门（抽象类），你想要个门我给不了（不能实例化），但我可以给你个具体的铁门或木门（多态）；而且只能是门，你不能说它是窗（单继承）；一个门可以有锁（接口）也可以有门铃（多实现）。 门（抽象类）定义了你是什么，接口（锁）规定了你能做什么（一个接口最好只能做一件事，你不能要求锁也能发出声音吧（接口污染））。

**$\textcolor{red}{抽象类主要用于关系密切的对象；而接口适合为不相关的类提供通用功能}$**

抽象类是一种特殊的类，它包含抽象方法，但也可以包含实例方法。

派生类必须实现的东西：抽象类是抽象方法，接口则是$\textcolor{red}{所有成员（不仅是方法包括其他成员）；}$

另外，接口有如下特性：
接口除了可以包含方法之外，还可以包含属性、索引器、事件，而且这些成员都被定义为公有的。除此之外，不能包含任何其他的成员，例如：常量、域、构造函数、析构函数、静态成员。一个类可以直接继承多个接口，但只能直接继承一个类（包括抽象类）。



## 集合

### ArrayList

![image-20221220002213446](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220002213446.png)



#### 操作：

初始化：

`ArrayList sd=new ArrayList();`

添加：

`public void Add()
        {
            qw.Add(1);
        }`

***$\textcolor{red}{该方法(Add)将返回添加了value处的索引值}$***

访问：按索引即可

注意Arraylist中不区分类型，所以拿出来时要强转一下

`Student stu = (Student)studs[0];`

删除：remove，remove at，clear

遍历:

![image-20221220003055581](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220003055581.png)

(for int i=0;i<studs.Count()) 也是可以的



### Hashtable

增加与删除元素

◦void Add(object key, object value)

◦void Remove(object key)

获取元素的方法

◦通过key获得value

如：Student stu = (Student)htStuds[1001];



### 泛型

与Arraylist相比，对类型有了要求，更加安全

将类型作为参数，即参数化类型

◦泛型类：class MyClass<T>

◦泛型方法：void Swap<T>(ref T a, ref T b )

#初始化：

`List<int> qw = new List<int>();`



## 异常处理

### try—catch语句

![image-20221220091833314](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220091833314.png)



`try
{
    int a = 1;
    int b = 0;
    Console.WriteLine((a / b).ToString());

}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}`

![image-20221220093717286](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220093717286.png)

### throw语句

个人理解：用于try的函数里面，抛出故障，然后catch

这里throw的内容可以自己定义

#### throw内容的自定义：

![image-20221220102514460](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220102514460.png)

继承Exection类，重写Message方法即可。

#### 具体使用：

`//try
//{
//    调用函数f();
//    过程中可能throw错误
//}
//catch(Exception e)
//{
//    Console.WriteLine(e.Message);
//}`

![image-20221220104058097](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220104058097.png)

![image-20221220104119109](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20221220104119109.png)



