---
title: JavaSe1
date: 2022-02-22 23:11:01
update: 2022-02-22 23:11:01
tags:
  - Java
categories:
  - JavaSE
keyword: Java 
top_img: https://s4.ax1x.com/2022/02/23/bpd5o8.jpg
cover: https://w.wallhaven.cc/full/72/wallhaven-72m513.png
copyright_author: Year21
copyright_url: http://year21.top/2022/02/22/JavsSe1/
---

## 菜鸟的第一个Java程序HelloWorld

1. 源码文件

~~~java
/*
1.java规范了三种注释方式
单行注释
多行注释
文档注释（Java特有）
2.单行注释和多行注释的作用：
①对所写的程序进行解释说明，增强可读性，便于方便自己，同时方便别人
②可以对已完成的代码进行调试

3.单行注释和多行注释的内容不参与编译。
编译生成的.class结尾的字节码文件不包含注释的信息

4.多行注释里面不可嵌套注释。
*/ 


/**
文档注释：
@author Ssk
@version jdk8.0 
这是今天学习的第一个java程序

*/

public class HelloJava {
	/*多行注释：
	如下的main方法是程序的入口！
	mian的格式是固定的！
	*/
	public static void main(String[] args) {
		//单行注释：如下的语句表示输出到控制台 
		System.out.println("Hello World!");
	}
}

~~~

2. 验证

​	①在dos窗口下利用cd以及dir指令进入源码保存的路径文件夹

​	②输入javac HelloWord.java回车编译得到HelloChina.class的字节码文件

​	③输入java HelloChina回车进行运行

​	④dos窗口回显Hello World！则代表成功

​	⑤否则根据提示进行排查

3. 注意事项  

​	输入javac HelloWorld.java 容易出现的问题：

​	①出现找不到文件（无法加载主类） 有可能是输入的指令名称错误或者没有在java的路径下

​	②javac 后面的字母有时没有严格区别大写也能运行是因为javac还是处于windows层面，在windows下大小写字母都被视为同一个文件，而在java环境下严格区

别大写字母

### 关于Java程序Hello World的补充

~~~java
/*
对第一个Java程序的编写总结
1.Java程序编写-编译-运行
编写：将已经编写好的java代码保存在以".java"为后缀的源文件中
编译：利用"javac.exe"命令对保存好的源文件进行编译。格式：javac 源文件名字
运行：利用"java.exe"命令对编译后产生的以".class"为后缀的字节码
		文件进行处理。格式：java 类名

2.
一个java源文件中可以声明多个class，但只有一个类（class）可以声明为public。
此外声明为public的类必须与源文件名字相同

3.程序的入口是main()方法，它的格式是固定的
（）内的中括号 和args是可以变的 尤其是String这个固定的类名

4.输出语句
System.out.println();  先输出数据，后换行
System.out.pront();  只输出数据，不换行

5.每一个执行语句都以";"结尾

6.编译的过程
.class为后缀的字节码文件编译后可以产生多份
字节码文件名与Java源文件的类名一致


*/


public class Hello{
	public static void main(String[] args) {//public static void main(String[] args) {//arguments;参数
		System.out.println("Hello World!");
		System.out.println();//换行
		System.out.print("Hello World!");
	}
}


class Person{

}

class animal{

}

~~~

---

### 练习题

1. JDK，JRE,JVM三者的关系，以及JDK,JRE包含的主要结构都有哪些

三者层层包含，由外到内分别是JDK,JRE,JVM

JDK=JRE+java的开发工具（javac.exe，java.exe，javadoc.exe）

JRE=JVM+java核心类库（JavaSE）

2.配置Path的意义，如何配置

希望在任何文件路径下都能运行java的开发工具。

配置：在系统变量中建议JAVA_Home的变量，变量值为bin的上层目录，即jdk的安装路径

并在path变量中添加两个%JAVA_HOME%\bin，%JAVA_HOME%\jre\bin

3.常用的命令行

cd  rd md dir exit del cd.. cd /d 

4.创建如下的类，使其运行输出

姓名 ：

性别：男

家庭地址：广东

创建Java文件：work.java

class定义的类名跟源文件名不一定要一样

有一种情况则要保持一直，即class前面带了public

正如下方的例子所示。

~~~java
public class work{
		public static void main(String[] args){
				System.out.println("姓名：");
				System.out.println();
				System.out.println("性别：男");
				System.out.println("家庭地址：广东");
 }
}
~~~

5.编译和运行上述代码的指令

编译：javac work.java

运行：java.work

---

## 基本语法

### Java注释(Comnet)的分类

- 单行注释  //
- 多行注释  /*    */
- 文档注释（ Java特有）  /**   */

文档注释的内容可以被JDK所提供的工具Javadoc所解析，可以生成一套以网页形式体现的该程序的说明文档。

演示 javadoc -d 生成的文件名 -author -version 源码文件名字.java

---

### 关键字（keyword)

定义：被Java语言赋予了特殊含义，用做专门用途的字符串(单词),关键字中所有的字母都是小写。

三个严格意义上不算关键字的关键字：**true  false  null**

---

### 保留字（reserved word）

定义：现有java版本尚未使用，但后续版本有可能作为关键字使用。

自己命名标识符要避开这些保留字：goto const

---

### 标识符

定义：java对各种变量、方法和类等要素命名使用的字符序列

技巧：凡是自己命名的地方都统称为标识符

比如：**类名，变量，方法，接口名，包名**等等

#### 命名规则

**1.由26个字母大小写，0-9，_或者$组成**

**2.数字不可以开头**

**3.不可以使用关键字和保留字，但允许包含关键字和保留字**

**4.java中严格区分大小写字母，长度则无限制**

**5.标识符不能含有空格**

以上规则不遵守则无法通过编译！

#### java名称规范

**包名：多单词组成的所有字母都是小写：xxxyyyzzz**

**类名、接口名：多单词组成时，所有的单词首字母大写：XxxYyyZzz**

**变量名、方法名：多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大写：xxxYyyZzz**

**常变量：所有字母都大写。多单词时每个单词用下划线连接：XXX_YYY_ZZZ**

以下规则不遵守也能通过编译，但不建议！

注意事项 类名命名尽量做到见名知意

##### 变量

变量是程序中最基本的存储单元，包括了***变量类型、变量名和存储的值***

1. 格式：数据类型 变量名=变量值
2. 说明：①变量必须先声明，再使用。②变量都定义在其作用域内。在作用域内，它是有效的。
3. **同一个作用域内，不能声明两个同名的变量**

---

### 数据类型

1. 基本数据类型

- 整型：byte   short   int   long

- 浮点型：float  double

- 字符型 ：char

- 布尔型：boolean

2. 引用数据类型

- 类(class)

- 接口(interface)

- 数组(array)

2. 变量在类中声明的位置

- 成员变量（属性）
- 局部变量


补充：整型数据的区别在于占用存储空间的不同

1byte=8bit位  

- byte的范围：-128~127
- 声明long型变量，必须以“l”或者“L”结尾
- 通常 ，**定义整型，使用int型**


#### 整形

byte（1字节）\short（2字节）\int（4字节）\long （8字节）

#### 浮点型

float(4字节) 单精度 double(8字节) 双精度 精度是float的两倍，**声明float的常量要以"f"或者"F"结尾**

**通常，在定义浮点型，使用double型**

### 字符型

char（1字符=2字节） 格式：char c1='a'  字符内部只能写一个字符。

表示方式：1.声明一个符号 2.转义字符 3.直接使用	Unicode值来表示字符型常量

#### 布尔型

只能取两个值之一：true 、false ，常常在条件判断、循环结构中使用

**\n转义字符前面加\斜杠代表这个转义字符的换行不再起作用**

在”“两个”前面各自添加一条斜杠\是为了起强调作用，而不影响运行。

#### 数据类型之间的运算规则

只包含了七种类型，不包含boolean型

自动类型提升

结论：当容量小的数据类型和容量大的数据类型的变量做运算时，结果自动提升为容量大的数据类型

byte、short、char-->int-->long-->float-->double

特别的：当byte、short、char三种类型的变量做运算时，结果为int的数据类型

即便是byte、short、char这三类同类的变量做 运算时，结果也是以int的数据类型

强制类型转换

自动类型提升的运算的逆运算

1.需要使用强转符：（）

2.强制转换类型有可能导致精度损失

---

### String类型变量使用

1. String属于引用数据类型，翻译为：字符串
2. 声明String类型变量，使用一对“”
3. String可以和8中基本数据类型变量做运算，且运算只能是连接运算。凡是在String后面的运算的+号都起连接作用，而不是相加作用。
4. 运算的结果依然是String类型,

**补充 在字符型 char 中 'a'=97    'A'=65**

---

### 关于进制转换

二进制：0，1 满2进1 以0b或者0B开头

十进制：0-9，满10进1

八进制：0-7，满8进1，以数字0开头表示

十六进制：0-9及A-F，满16进1，以0x或者0X开头表示。

所有数字在计算机底层都以二进制形式存在

所有的数值，不管正负，底层都以补码的方式存储

#### 二进制

二进制中最左边的是为最高位，0代表正数,1代表负数。

原码，反码，补码的说明：

正数：三码合一

负数：原码；直接将一个数值转换为二进制。最高位是符号位

负数的反码：除了符号位以外，是对原码按位取反，只是最高位（符号位）确定为1

负数的补码：其反码加1

#### 十进制

十进制==>二进制：处2取余的逆

#### 八进制

二转八：每三个byte按2的0次幂，2的一次幂，2的二次幂计算

八转二：则把每个八进制的按上面拆开。

#### 十六进制

二转十六：每四个byte按2的0次幂，2的一次幂，2的二次幂计算，2的三次幂。

十六转二：则把每个八进制的按上面拆开。

---

### 运算符

定义：运算符是一种特殊的符号，用以表示数据的运算、赋值和比较等。

#### 算术运算符

**+ - + - * / % （前）++ （后）++ （前）-- （后）--   +**

1. 取模（取余）运算：结果的符号与被模数的符号相同。

开发当中经常使用%来判断能否被除尽的情况。

2. %取得位数，/去掉位数，187三位数，%10取得最后一位，%100取得最后两位，%1000取得最后三位。

3. （变量前）++变量 ：先自增1，再运算。

​		（变量后）变量++ ：先运算，后自增1。

​		判断是看这个++在变量的哪个位置。	减法同理，在此不做说明。

##### 四位数输出分位的练习

~~~java
public class AriExer{
		public static void main(String[] args){
			int num = 1440; 
			int qian = num / 1000;
			int bai = num / 100 % 10;
			int shi = num / 10 % 10;
			int ge = num /1 % 10 ;	
			System.out.println("千分位为:" + qian);
			System.out.println("百分位为:" + bai);
			System.out.println("十分位为:" + shi);
			System.out.println("个分位为:" + ge);
		
		}


}
~~~

#### 赋值运算符

略

### 比较运算符（关系运算符）

**==相等于  !=不等于  <小于    >大于      <=           小于等于       >=大于等于     instanceof 检查是否是类的对象**

1. 比较运算符的结果都属于boolean布尔型的数据类型
2. **>   <   >=   <=：只能使用在数据类型的数据之间**
3. **== 和 ！=：不仅可以使用在数值类型数据之间，还可以使用在其他引用类型变量之间**
4. 区分== 和 = 

#### 逻辑运算符

&逻辑与   |逻辑或  ！逻辑非

&&短路与  ||短路或  ^逻辑异或

![逻辑运算符](https://s4.ax1x.com/2022/01/30/HPi5p8.png)

逻辑与、短路与两者相同，输出相同的结果，两者不同输出 false

逻辑或、短路或两者相同，输出相同的结果，两者不同输出 true

逻辑非 结果取反

逻辑异或 两者相同输出相反结果，两者不同输出 true

- 逻辑运算符的操作都是boolean类型的变量。

区分&和&&

1. 相同点：&与&&的运算结果相同
2. 相同点2：当符号左边是true时，二者都会执行符号右边的运算
3. 不同点，当符号左边是false时，&继续执行符号右边的运算，&&不再执行符号右边的运算。 
4. 开发中推荐使用&&



区分 | 和||

1. 相同点：|和||的运算结果是相同的
2. 相同点：当符号左边是false时，二者都会执行符号右边的运算
3. 不同点：当符号左边是true时，|继续执行符号右边的运算，而||不再执行符号右边的运算
4. 开发中推荐使用||


#### 位运算符

位运算符是直接对整数的二进制进行的运算。

<< 左移    >>右移    >>>无符号右移      &与运算      |或运算     ^异或运算           ~取反运算

- 位运算符操作的都是整型的数据

1. **<<:在一定的范围内，每向左移1位，相当于*2**
2. **>>:在一定的范围内，每向右移1位，相当于/2** 

二进制中最左边的是为最高位，0代表正数,1代表负数。

**<< 空位补0，被移除的高位丢弃，空缺位补0.**

**>>被移位的二进制最高位是0，右移后，空缺位补0：最高位是1，空缺位补1**

**>>>被移位二进制最高位无论是0或者是1，左移后，空缺位都由0补**

实现最高效的2*8的方式

2<<3 或者 8<<1

- 练习交换两个变量

~~~java
String place1 = "广东", place2 = "江苏",tempPlace = "中转";//定义一个临时变量
            //利用临时变量交换赋值
            tempPlace = place1;
            place1 = place2;
            place2 = tempPlace;
            //输出最终交换的结果
            System.out.println("起点:" + place1);
            System.out.println("终点:" + place2 );

~~~

#### 三元运算符

格式（条件表达式）？表达式1：表达式2

①条件表达式的结果为Boolean类型

②根据表达式真或假，决定执行表达式1，还是表达式2
如果表达式为true，则执行表达式1
如果表达式为false，则执行表达式2

③表达式1和表达式2要求是一致的

④三元运算符可以嵌套使用

凡是可以使用三元运算符的地方都可以改写成 if-else。

反之，不成立。如果程序既可以使用三元运算符，也可以使用if-esle，那么优先使用三元运算符。原因是简洁，执行效率高。

#### 运算符的优先级

PS：想那个先算 直接套（）

---

### 流程控制

流程控制采用结构化程序设计规定的三种基本流程控制。

- 顺序结构

程序自上到下逐行执行，中间没有任何判断和跳转

- 分支结构

根据条件，选择性执行代码。分别有if-else和switch-case的两种分支语句

- 循环结构

根据循环条件，重复性执行某段代码。分别有while、do-while、for三种循环语句

 jdk1.5提供了foreach循环，方便的遍历集合、数组元素

#### if-else的三种语句格式：

1. if（条件表达式）{

​		执行语句

​		}

2. if（条件表达式）{

​		执行语句

​		}else {

​		条件表达式

​		}

3. if（条件表达式）{

​			执行语句

​		}else if（条件表达式）{

​			执行语句

​		}else if（条件表达式）{

​			执行语句

​		}

​		。。。

​		else {

​			执行语句

​		}

#### 相关练习

~~~java
    public static void main(String[] args){
        //两个变量交换的练习
       String place1 = "广东", place2 = "江苏",tempPlace = "中转";//定义一个临时变量
            //利用临时变量交换赋值
            tempPlace = place1;
            place1 = place2;
            place2 = tempPlace;
            //输出最终交换的结果
            System.out.println("起点:" + place1);
            System.out.println("终点:" + place2 );

        //利用三元运算符或者if-else将三者最大值输出
        int num1 = 2, num2 = 4, num3 = 6;
        int max = (num1 > num2)? num1 : num2;
        int max1 = (num2 > num3)? num2 : num3;
            System.out.println(max1 );


        int i1 = 2, i2 = 4, i3=6;
        if (i1 > i2 && i1 >i3) {
            System.out.println("三个数字当中最大值:" + i1);
        }else if (i2 >i1 && i2 >  i3) {
            System.out.println("三个数字当中最大值:" + i2);
        }else if (i3 >i1 && i3>  i2) {
            System.out.println("三个数字当中最大值:" + i3);
        }
        else {
            System.out.println("没有合适的答案" );
        }

        //通过if-else二选一输出定义的double变量的数据
        double d1 = 10.1 , d2 = 15.4;
        if (d1 > 10 && d2 < 20) {
            System.out.println(d1 + d2);
        }else {
            System.out.println(d1 * d2 );
        }
   	}	
~~~

####  if-else补充

1. else的结构是可选的。

2. 针对于条件表达式；

   如果多个文件表达式之间是互斥关系（或者没有交集的关系），哪个判读和执行语句声明在上面还是下面

   都不影响

​       如果多个条件表达式之间有交集关系，需要根据实际情况，考虑清楚应该将哪个结构声明在上面

​		如果多个条件表达式之间有包含关系，需要将范围小的声明在范围大的上面，否则，范围小的就受影响。

3. if-else是可以嵌套使用的。if-else是就近原则
4. 如果if-else结构中的执行语句只有一行时，{}是可以省略的，但不建议使用

#### Switch-case

结构:

switch（表达式）{

case 常量1：

​		执行语句1：

​		//break；

case 常量2：

​		执行语句2：

​		//break；

....

default；

​		执行语句；

​		//break；

}

说明

①根据switch的表达中的值，依次匹配各个case中的常量。一旦匹配成功，则进入相应的case结构中，调用执行语句。当调用执行语句完成后，则仍然继续向下执行其他case结构中的执行语句，直到遇到break关键字或者次switch-case结构末尾结束为止。

②break关键字可以使用在此结构中，执行到此关键字，则跳出此swistch-case结构

③switch结构中的表达式只能是以下6种数据类型之一：byte、short、char、int、枚举类型（jdk5.0新增）、String类型（jdk7.0新增）

④case之后只能声明常量，不能声明范围。

⑤braek关键字是可选的。

⑥default：相当于if-else种的else。

⑦switch-case结构中多个case的执行语句相同，则可以进行合并处理

⑧凡是可以使用switch-case的结构，都可以转换为if-else。反之，则不行。

⑨当写分支结构时，发现两种结构都可以使用，（同时，switch中表达式的取值情况不太多），则优先使用switch-case。

---

### Scanner 类

具体实现步骤

1. 导包：import java.util.Scanner ; //import 导入
2. Scanner的实例化：Scanner scan = new Scanner（system.in）
3. 调用Scanner类的相关方法（next（）/nextXxx（）），来获取指定类型的变量。一般读取什么类型的值就定义什么类型的变量。

注意：需要根据相应的方法，来输入指定类型的值。如果输入的数据类型与要求类型不匹配时，会报异常；InputMisMatchExce导致程序终止。

---

### 循环结构

循环语句的四个要素

①初始化条件（int_statement）

②循环条件（test_exp）-->是boolean类型

③循环体（body_statement）

④迭代条件（alter_statement）

#### for循环

结构：

for（①；②；④）{

​				③

}



#### while循环

结构

①

while（②）{

​	     	 ③；

​			  ④；

}

说明 

1. while不能缺少迭代部分，也就是④，才能避免出现死循环的情况。
2. for循环和while循环是可以相互转换的。
3. for循环的①只能在作用域内使用，而while的①在作用域之外还能调用。



#### do-while循环

结构：

①

do{

​		③；

​		④；

}while(②)；

执行过程：①-③-④-②-③-④-...-②

说明：do-while至少循环一次循环体，大部分情况下 for和while使用更多，较少使用do-while。



##### 补充知识

1. 不在循环条件部分限制的结构：for(;;)或while（true）
2. 结束循环的方式：①循环条件返回false ②在循环体中执行break。

#### 嵌套循环

定义：将一个循环结构A声明在另一个循环结构B的循环体中。

1. 外层循环

2. 内层循环

3. 内层循环结构遍历一遍，只相当于外层循环循环体执行了一次。 

   **双层for循环的计算： 内层循环m次 ，外层循环n次，实际总计实行了m*n次循环。**

4. 技巧：外层循环控制行数，内层循环控制列数

##### 特殊关键字的使用

不同点：

①break使用范围是在switch-case和循环结构中，作用是结束当前的循环。

②continute使用在循环结构中，作用是结束单次循环

相同点：关键字后面不能声明执行语句。


```java
	labe1:for (int i =1;i <4;i++){
       for (int j =1;j <= 10;j++){
       if (j % 4 ==0){
      //break;//默认跳出包裹此关键字最近一层的循环
     //continue;//默认跳出包裹此关键字最近一层的一次循环

     //break labe1;//结束指定标识的一层循环结构
     continue labe1;//结束指定标识的一层循环结构的当次循环
```

---

## 数组

1. 数组（array):是多个相同类型数据按一定的顺序排列的集合，并使用一个名字命名，并通过编号的方式对这些数据进行统一的管理。

2. 数组的常见概念

   数组名

   元素

   角标、下标（或索引）

   数组的长度：元素的个数

3. 数组的特点：①数组是有序排列的

   ②数据属于引用数据类型，数据的元素可以是任意类型。

   ③创建数组对象会在内存中开辟一整块连续的空间。不连续的叫链表。

   ④数据的长度一旦确定，就不可以修改。

4. 数组的分类

①按照维数:一维数组。二维数组....

②按照数组元素的类型：基本数据类型元素的数组、引用数据类型元素的数组..

### 一维数组

①一维数组的声明和初始化

```java
  //①一维数组的声明和初始化
     
        int[] ids;//声明
        //1.1静态初始化：数组的初始化和数组元素的赋值操作同时进行
        ids = new int[]{1001, 1002, 1003, 1004};
        //1.2动态初始化：数组的初始化和数组元素的赋值操作分开进行
        String[] names = new String[5];
```

总结：数据一旦初始化完成，数组的长度也就确定了。

②如何调用数组的指定位置的元素：通过角标的方式调用

数组的角标（或索引0是从0开始的，到数组的长度-1结束的。

```java
        //②如何调用数组的指定位置的元素：通过角标的方式调用
        names[0] = "zhang";
        names[1] = "san";
        names[2] = "li";
        names[3] = "si";
        names[4] = "wu";
```

③如何获取数组的长度

```java
        //③如何获取数组的长度
        //属性：length
        System.out.println(names.length);
        System.out.println(ids.length);
```


④如何遍历数组

```java
        //④如何遍历数组
        for (int i = 0; i < names.length; i++) {
            System.out.println(names[i]);
        }
```


⑤数组元素的默认初始化值

1. 数组元素是整形：0
2. 数组元素是浮点型：0.0
3. 数组元素是char型：0或者是'u0000'，而非‘0’
4. 数组元素是boolean型：false
5. 数组元素是引用数据类型：null

```java
        int[] arr = new int[4];
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]);
        }
        System.out.println("************");

        short[] arr1 = new short[5];
        for (int i = 0; i < arr1.length; i++) {
            System.out.println(arr1[i]);
        }

        float[] arr2 = new float[5];
        for (int i = 0; i < arr2.length; i++) {
             System.out.println(arr2[i]);
            }

        char [] arr3 =new char[5];
        for (int i = 0; i < arr3.length; i++) {
            System.out.println("----" + arr3[i] + "******");
        }
        if (arr3[0] ==0){
            System.out.println("你好");
        }

        System.out.println("************");
        boolean [] arr4 =new boolean[5];
            System.out.println(arr4[0]);

        System.out.println("************");
        String [] arr5 =new String[5];
        System.out.println(arr5[0]);
        if(arr5[0] == null) {
            System.out.println("shuchu");
        }
```


⑥数组的内存解析

栈（stack）：局部变量。栈的特点是先进后出

堆（heap）：new出来的结构：对象、数组

方法区：常量池，静态域，类信息，即时编译后的代码

一维数组的内存解析：如图

![img](https://s4.ax1x.com/2022/02/22/bpUmFg.png)


### 二维数组

二维数组：可以看成是一维数组array1又作为另一个一维数组array2的元素而存在的

①二维数组的声明和初始化

```java
                //①二维数组的声明和初始化
                int[] arr = new int[]{1,2,3};//一维数组
                //二维数组静态初始化
                int[][] arr1 = new int[][]{{1,2,3},{4,5},{6,7,8}};
                //二维数组动态初始化1
                String[][] arr2 = new String[3][2];
                //二维数组动态初始化2
                String[][] arr3 = new String[3][];
                //不算标准的写法
                int [][]  arr5 = {{1,2,3},{4,5},{6,7,8}};
                int [] arr4 [] = new int[][] {{1,2,3},{4,5},{6,7,8}};
```


②如何调用数组的指定位置的元素

```java
                System.out.println(arr1[0][1]);//2
                System.out.println(arr2[1][1]);//nu11

                arr3[1] = new String[4];
                System.out.println(arr3[1][0]);
```

③如何获取数组的长度

```java
                System.out.println(arr4.length);//3
                System.out.println(arr4[0].length);//3
                System.out.println(arr4[1].length);//2
```


④如何遍历二维数组

```java
                  for(int i = 0 ;i < arr4.length;i++) {

                    for (int j = 0; j<arr4[i].length;j++){
                        System.out.print(arr4[i][j] + "  ");
                    }
                        System.out.println();
                }
```


⑤数组元素的默认初始化值

二维数组分为外层数组的元素，内层数组的元素

```
 int [] arr =new int [4][3];
 外层元素：arr[0],arr[1]等等
 内层元素：arr[0][0],arr[1][2]等等
```

针对初始化方式一，比如:int [] [] arr = new int [4][3] 

外层元素的初始化值为：地址值

内层元素的初始化值为：与一维数组初始化情况相同

针对初始化方式二，比如:int [] [] arr = new int [4][] 

外层元素的初始化值为：null

内层元素的初始化值为：不能调用，否则报错

```java
                //⑤数组元素的默认初始化值
                int arr6 [][] = new int[4][3];
                System.out.println(arr6 [0]);//地址值
                System.out.println(arr6 [0][0]);//0

                //特殊情况
                double [][] arr7 = new double[4][];
                System.out.println(arr7 [1]);//null
                System.out.println(arr7 [1][0]);//空指针，报错
```


⑥二维数组的内存解析

![内存解析](https://s4.ax1x.com/2022/02/22/bpUgte.png)


### 数组练习

```java
     //练习2
        int sum = 0;//记录数组的和
        int[][] arr = new int[][]{{3,5,8},{12,9},{7,0,6,4}};
        for (int i =0;i < arr.length;i++){
            for (int j=0;j<arr[i].length;j++){
                sum += arr[i][j];
            }
        }
        System.out.println("该二维数组的总和为：" + sum);

//       练习3
//       int [] x,y[] 实际上等于 int [] x, int [][] ；

        //杨辉三角练习
        //1.声明并初始化二维数组
        int [][] yangHui = new int[10][];
        //2.给数组的元素赋值
        for(int i = 0;i< yangHui.length;i++){
            yangHui[i] = new int[i +1];
        //2.1给首末元素赋值
            yangHui[i][0] = yangHui[i][i] = 1;
        //2.2给每行的非首末元素赋值
//        if(i>1) {
            for (int j = 1; j < yangHui[i].length - 1; j++) {
                yangHui[i][j] = yangHui[i - 1][j - 1] + yangHui[i - 1][j];
            }
//        }
        }
        //3.遍历二维数组
        for (int i =0;i< yangHui.length;i++){
            for (int j = 0;j< yangHui[i].length;j++){
                System.out.print(yangHui[i][j] + "  ");
            }
            System.out.println();
        }

        //经典笔试题

       int[] arr1 = new int[6];
        //[0.0,1.0)=>[0.0,30.0)=>[1.0,31.0)=>[1,30]
        for (int i = 0; i < arr1.length; i++) {
            arr1[i] = (int) (Math.random() * (30 - 1 + 1) + 1);
            for (int j = 0; j < i; j++) {
                if (arr1[i] == arr1[j]) {
                    i--;
                    break;
                }
            }
            System.out.println(arr1[i]);
        }
```

 1.定义一组元素为10的数组，并从1-30之间抽取随机字数给数组元素赋值

```java
         int[] arr2 = new int[10];
        for (int i = 0; i < arr2.length; i++) {
            arr2[i] = (int) (Math.random() * (99 - 10 + 1) + 10);
            System.out.print(arr2[i] + "\t" );
        }
        System.out.println();
```

2.求数组的最大值

```java
        int max = arr2[0];
        for(int i =0 ;i < arr2.length;i++){
            if (max < arr2[i]) {
                max = arr2[i];
            }
        }
        System.out.println("最大值为：" + max);
```

3.求数组的最小值

```java
        int min = arr2[0];
        for (int i = 0;i < arr2.length;i++){
            if (min >arr2[i]){
                min =arr2[i];
            }
        }
        System.out.println("最小值为：" + min);
```

4.数组元素的总和

```java
         int sum = 0;
        for (int i =0; i < arr2.length;i++){
            sum +=arr2[i];
        }
        System.out.println("和值为：" + sum);
```


5.数组的平均数

```java
        int average = sum / arr2.length;
        System.out.println("均值为：" + average);
```


------

arrarray1和array2的关系是：array1和array2的地址值相同，都指向了堆空间中唯一的一个数组实体。

```java
             //定义两个数组
            int[] array1 ,array2;
            //显示数组array1的内容
            array1 = new int[]{2,3,5,7,11,13,17,19};
            for (int i = 0;i< array1.length;i++){
                System.out.print(array1[i] + "\t");
            }
            System.out.println();

            //赋值array2变量等于array1，不能称作数组的复制，只是array2复制了array1的地址值。
            array2 = array1;
            //修改array2的偶索引元素，使其索引值等于（如array[0]=0，array[2]=2，
            for (int i =0;i<array2.length;i++){
                if (i % 2 == 0 ){
                    array2[i] = i;
                }
            }
            System.out.println();

            //打印出数组array1；
            for (int i = 0;i< array1.length;i++) {
                System.out.print(array1[i] + "\t");
            }



            //补充 真正意义上array2对array1数组的复制
            array2 = new int[array1.length];
            for (int i =0; i< array2.length;i++){
                array2[i]=array1[i];
            }
```


### 数组的复制、反转、查找

```java
//数组的复制(区别于数组变量的赋值：arr1 = arr)
        String[] arr1 = new String[arr.length];
        for (int i = 0;i < arr1.length;i++){
            arr1[i] = arr[i];
            System.out.print(arr1[i] + "\t");
        }
        System.out.println();

        //数组的反转
        //方式一：
        for(int i = 0;i < arr.length /2 ;i++){
            String temp = arr[i];
            arr[i] = arr[arr.length -i -1];
            arr[arr.length -i -1 ] = temp;
        }
        //方式二：
//        for(int i = 0,j = arr.length;i< j;i++,j--){
//          String temp = arr [i];
//            arr [i] =arr[j];
//            arr [j] = temp;
//        }

        //遍历数组
        for (int i = 0;i < arr.length;i++){
            System.out.print(arr[i] + "\t");
        }

        System.out.println();

        //查找（或搜索）
        //1.线性查找
        String dest = "BB";
        boolean isFlag = true;
        for(int i = 0;i < arr.length;i++){
            if (dest.equals(arr[i])){
                System.out.println("找到了指定元素，位置为：" + i);
                isFlag = false;
                break;
            }
        }
        if(isFlag) {
            System.out.println("很遗憾，没找到");
        }

        //2.二分法查找的前提是：所要查找的数组必须有序(熟悉)
        int[] arr2 = new int[]{-98,-34,2,34,54,66,79,105,210,333};
        int dest1 = -34;
        int head = 0;//初始的首索引
        int end = arr2.length -1;//初始的末索引
        boolean isFlag1 = true;
        while(head <= end){

         int middle = (head + end ) / 2;
            if (dest1 == arr2[middle]){
               System.out.println("找到了指定元素，位置为：" + middle);
               isFlag1 = false;
               break;
            }else if (arr2[middle] > dest1){
                end = middle -1;
            }
            else {
                head =middle +1;
            }
        }
        if(isFlag1) {
            System.out.println("很遗憾，没找到");
        }
```


### 数组的冒泡排序的实现

```java
     int[]  arr =new int[]{43,32,76,-98,0,64,33,-21,32,99};
        //冒泡排序
        for(int i =0 ;i<arr.length-1;i++){//元素是8 最多只进行了7轮就完成了
            for (int j = 0; j< arr.length -1 -i;j++){//没进行一轮就会少一个数，每轮都把最大的放在了右边
                if(arr[j] > arr[j+1]){
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1 ] = temp;
                }
j
            }
        }

        for(int i =0 ;i<arr.length;i++){
            System.out.print(arr[i] + "\t");
        }
```


### 数组的快速排序的实现

low比5指向的要小，比5小继续走，比5大就停止。

high比5指向的要大，比5大继续走，比五小就停止。

当low的大于high就停止查找，并将high指针的值与pivot的值交换结束。

![快速排序演示](https://s4.ax1x.com/2022/02/22/bpUf1A.png)




------

![数组的工具类](https://s4.ax1x.com/2022/02/22/bpU4Xt.png)


```java
        //1.boolean equals(int[] a ,int[]b);判断两个数组是否相等
        int[] arr1 =new int[]{1,2,3,4};
        int[] arr2 =new int[]{1,3,2,4};
        boolean isEquals = Arrays.equals(arr1,arr2);
        System.out.println(isEquals);

        //2.String toString(int [] a) 输出数组信息
        System.out.println(Arrays.toString(arr1));


        //3.void fill(int[] a,int val)将指定的值填充到数组当中
        Arrays.fill(arr1,10);
        System.out.println(Arrays.toString(arr1));

        //4.void sort(int[] a) 对数组进行排序
        Arrays.sort(arr2);
        System.out.println(Arrays.toString(arr2));

        //5.int binarySearch(int[] a,int key)二分查找
        int[]  array3 = new int[]{2,3,5,7,11,13,17,19};
        int index =Arrays.binarySearch(array3,7);
        if(index >=0){
            System.out.println(index);
        }else {
            System.out.println("未找到");
        }
```


### 数组中常见异常

1.数组角标越界的异常：ArrayIndexOutOfBoundsExcetion

```java
        //1.数组角标越界的异常：ArrayIndexOutOfBoundsExcetion
        int[] arr = new int[] {1,2,3,4,5};
            for (int i= 0;i <= arr.length;i++){
                System.out.println(arr[i]);
            }
            System.out.println(arr[-2]);
```


2.空指针异常：NullPointerException

```java
            //2.空指针异常：NullPointerException
            //情况一
            int [] arr1 = new int[]{1,2,3};
            arr1 =null;
            System.out.println(arr1[0]);

            //情况二
            int [][]  arr2 = new int[2][];
            System.out.println(arr2[0][0]);

            //情况三
            String [] arr3 = new String[]{"AA","BB","CC"};
            arr3[0] = null;
            System.out.println(arr3[0].toString());
```

---

## 数据结构（浅知识）

1. 数据与数据之间的逻辑关系：集合、一对一、一对多、多对多。
2. 数据的存储结构：

线性表（一对一）：顺序表（比如：数组）、链表、栈、队列。

树形结构（一对多）：二叉树

图形结构（多对多）：有向图、无向图....

算法：排序算法、搜索算法

排序算法分类：内部排序和外部排序。

内部排序算法=>1.选择排序：直接选择排序、堆排序  2.交换排序：冒泡排序、快速排序  3.插入排序：直接插入排序、折半插入排序、shell排序（希尔排序）4.归并排序  5.桶式排序  6.基数排序

算法的五大特征：输入、输出、有穷性、确定性、可行性。

衡量算法的优劣性：

1. 时间复杂度：分析关键字的比较次数和记录的移动次数
2. 空间复杂度：分析排序算法中需要多少辅助内存
3. 稳定性：若两个记录A和B的关键字值相等，但排序后A、B的先后次序保持不变，则称这种排序算法是稳定的。

时间复杂度的蓝框值记一下。

![算法的时间复杂度](https://s4.ax1x.com/2022/02/22/bpUHAS.png)


---

## 面向对象上

### 面向对象的主线

1. java类及类的成员：属性、方法、构造器、代码块、内部类
2. 面向对象的三大特征：封装、继承、多态、（抽象性）
3. 其他关键字：this、super、static、final、abstract、interface、package、import



如何理解面向过程和面向对象？以“人把大象装进冰箱”为例

1. 面向过程：强调的是功能行为，以函数为最小单位，考虑怎么做

①把冰箱门打开

②抬起大象，把大象塞进冰箱

③把冰箱门关上

2. 面向对象：强调了具备功能的对象，以类/对象为最小单位，考虑谁来做

```java
人{
    打开(冰箱){
        冰箱，打开();
    }
    抬起(大象){
        大象，进入(冰箱);
    }
    关闭(冰箱){
        冰箱，关闭()
    }
}

冰箱{
    打开（）{}
    关闭（）{}
}
大象{
    进入（冰箱）{
        
    }
}
```

### 面向对象的元素

#### 定义

类：对一类事物的描述，是抽象的、概念上的定义

对象：是实际存在的该类事物的每个个体，因此也称为实例（instance）

面向对象程序设计的重点是类的设计。

#### 设计类，就是设计类的成员。

属性= 成员变量=field=域、字段

方法=成员方法=函数=method  

创建类的对象=类的实例化=实例化类

#### 类和对象的使用（实例化）

①创建类，设计类的成员

②创建类的对象

③通过对象（名）调用类的属性和方法。

具体操作：

1. 调用对象的结构：属性、方法

2. 调用属性：”对象.属性“

3. 调用对象的方法：”对象.方法“

补充：如果在一个类中创建了多个 多个对象，每个对象都拥有独立的一套类的属性（非static的）。也就意味着修改其中一个的对象的属性，不会影响到另一个属性的值。

#### 内存解析

![内存解析](https://s4.ax1x.com/2022/02/22/bpUvXq.png)

### 类中属性的使用

属性（成员变量）与  局部变量

1. 相同点

①定义变量的格式一致：数据类型 变量名 = 变量值

②都是先声明，后使用

③变量都有其固定的使用域

2. 不同点：

①在类中声明的位置不同

属性：之间定义在类的一对{}内

局部变量：声明在方法内、方法形参、代码块内、构造器形参、构造器内部的变量

②关于权限修饰符的不同

属性：可以在声明属性时，指明其权限，使用权限修饰符

常用的权限修饰符：private、public、缺省、protected

局部变量：不可以使用权限修饰符。

3. 默认初始化值

属性：类的属性，根据其类型，都有默认初始化值。

​				整形（byte、short、int、long）:0

​				浮点型（float、double）：0.0

​				字符型（char）：0或者以‘\u0000’来表示

​				布尔型（boolean）：false

​				引用数据类型（类、接口、数组）：null

局部变量：没有默认初始化值。也就意味着在调用局部变量之前必须先显式赋值。 

​				 	需要注意的是形参在调用时赋值即可

4. 在内存中加载的位置不同

属性：加载在堆空间中（非static）

局部变量：加载在栈空间中

### 类中方法的使用

方法：描述类应该具有的功能

1. 例如：Math类：sqrt（）、random（）...

​			Scanner类：nextXxx（）...

​			Arrays类：sort（）、binarySearch()、toString（）、equals（）...

代码举例：

~~~java
public void eat(){}//不返回数据，无形参的void方法
public void sleep(int hour){}//不返回数据，有形参的void方法
public String getName(){}//返回数据，无形参的 返回值的类型方法
public String getNation(String nation){}//返回数据，有形参的 返回值的类型方法
~~~



![方法的分类](https://s4.ax1x.com/2022/02/22/bpap7T.png)

2. 方法的声明：

   格式：权限修饰符  返回值类型  方法名（形参列表）{

   ​									方法体

   }

​		static、final、abstract来修饰方法

3. 权限修饰符

java规定的4种权限修饰符：private、public、缺省、protected

4. 返回值类型

有返回值 与  无返回值

①区别：如果方法有返回值，则必须在方法声明时，指定返回值的类型。同时在方法中，必须使用return关键字返回指定类型的变量或常量。“return 数据”

如果方法没有返回值，则方法声明时，使用void来表示。一般在没有返回值的方法中不用使用return，非要使用只能用“ return； ”表示结束此方法的意思

②定义方法要不要返回值

​	1.看题目要求

​	2.根据经验判断

5. 方法名：属于标识符，遵循标识符的规则和规范，“见名知意”

6. **形参列表**：方法可以声明0个、一个或多个形参。

   ①格式：数据类型1 形参1，数据类型2，形参2，....

​		②定义方法判断是否需要形参

​			1.题目要求

​			2.根据经验判断

7. 方法体：方法功能的体现

---

### return关键字的使用

1. 使用范围：使用在方法体中
2. 作用：①结束方法 ②针对有返回值类型的方法，使用“  return 数据” 方法返回需要的数据
3. 补充：return关键字后面不可以声明执行语句

---

#### 方法的使用补充

1. 可以调用当前类的属性或方法

特殊的：方法A中调用了方法A----递归方法

2. 方法中不可以定义方法

---

### Jvm内存结构

发生在字节码文件编译完开始运行的环节。使用JVM中的类的加载器和解释器对生成的字节码文件进行解释运行。此时需要将字节码文件对应的类加载到内存中，才涉及了内存解析

在虚拟机栈中，局部变量储存在栈结构中，new出来的结构（数组、对象、对象的属性（非static））都储存在堆空间中。方法区则储存类的加载信息、常量池、静态域

---

**如何理解“万事万物皆对象”？**

1. 在java语言中，都将功能、结构等封装到类中，通过类的实例化，来调用具体的功能结构
2. 涉及到Java语言与前端html、后端数据库交互时，在java层面交互时，都体现为类、对象。



### 对象数组内存解析

①引用数据类型的变量只可能存储两类值：null 或 地址值（含变量的类型）

![内存分析](https://s4.ax1x.com/2022/02/22/bpaA39.png)

---

### 匿名对象的使用

1. 概念：创建的对象没有显式的赋给一个变量名，即为匿名对象
2. 特征：匿名对象只能调用一次
3. 应用场景：见代码

---

### 方法的相关补充

####  方法的重载

1. 概念：在同一个类当中，允许存在一个以上的同名方法，只需要它们的参数个数或者参数类型不同即可

满足“两同一不同”：同一个类、相同方法名。参数列表不同：参数类型不同，参数个数不同，参数顺序不同

2. 举例：Arrays类重重载的sort（）/binarySearch（）

3. 判断是否重载：跟方法的权限修饰符、返回值类型、形参变量名、方法体都没有关系。
4. 在通过对象调用方法时，如何确定某一个指定的方法

​		方法名--->参数列表

#### 可变个数的形参

1. 是属于jdk5.0的新增内容
2. ①格式：数据类型 ... 变量名

​		②当调用可变个数形参的方法时，传入的参数个数可以是0个，1个，2个等等

​		③可变个数形参的方法与本类中方法名相同，形参类型不同的方法之间构成重载。

​		④可变个数形参的方法与本类中方法名相同，形参类型也相同的方法之间不构成重载。也就是两者不能共存

​		⑤可变个数形参在方法的形参中，只能声明在末尾

​		⑥可变个数形参在方法的形参中，只能声明一个可变形参。

补充：1可变参数方法和固定参数方法同时满足执行条件时，会**优先执行固定参数方法** 。 ---- 固定参数方法优先级高于可变参数方法。

​			2 可变参数其实是一个 **数组** 

​			3 如果一个方法有多个参数，必须把可变参数放到**最后**。

​            4 **一个方法，只能有一个可变参数。**

#### 方法参数的值传递机制：值传递

- 关于变量的赋值：如果变量是基本数据类型，此时赋值的是变量所保存的数据值。

​					如果变量是引用数据类型，此时赋值的是变量所保存的数据的地址值。

1. 形参：方法定义时，声明小括号内的参数

​		实参：方法调用时，实际传递给形参的数据。

2. 值传递机制：

​			如果参数是基本数据类型，则实参赋给形参的是实参真实存储的数据值。

​			如果参数是引用数据类型，则实参赋给形参的是实参真实存储数据的地址值 (含变量的数据类型  )

![基本数据类型](https://s4.ax1x.com/2022/02/23/bpaenx.png)

![引用数据类型](https://s4.ax1x.com/2022/02/23/bpanHK.png)

#### 递归方法

定义：一个方法体内调用它自身。

②此方法中包含一种隐式的循环，会重复执行某段代码，但这种重复执行无需循环控制

需要注意的是：递归要往已知方向递归，否则就会形成无穷递归，类似死循环

n！-->（n的阶乘）是指从1、2……（n-1）、n这n个数的连乘积，即n！=1×2×……×（n-1）×n，在排列组合中常用到。

---

### 面向对象的特征

#### 封装性

1. 封装性的设计思想：把该隐藏的隐藏起来，该暴露的暴露出来。高内聚，低耦合。

2. 封装性的体现之一：将类的属性私有化（private）， 提供公共的方法（public）方法来获取（get）和设置（set）属性的值。

​	封装性的体现：①属性私有化 ②方法私有化（不对外暴露的私有方法）③单例模式（将构造器私有化）

3. 封装性的体现通过权限修饰符来配合

​	①4种权限修饰符（从小到大）：private、缺省、、protected、public

​	②4种权限可以修饰类的内部结构：属性、方法、构造器、内部类

​		但是对于类的修饰只能用缺省、public	

4. java提供了4种权限修饰符来修饰类及类的内部结构，体现类及类的内部结构在被调用时的可见性的大小

![权限修饰符](https://s4.ax1x.com/2022/02/23/bpa33d.png)

### 构造器(Constructor)的使用

1. 构造器(的作用：创建对象、初始化对象的属性
2. 说明：①如果没有显式的定义构造器，系统提供一个默认空参的构造器，空参构造器的权限修饰大小与它所在的类相同

​	②定义构造器的格式： 权限修饰符 类名（形参列表）{}

3. 一个类中定义的多个构造器可以构成重载
4. 一旦显式的定义了类的构造器之后，系统就不再提供默认的空参构造器

5. 一个类中至少存在一个构造器
6. ***属性赋值的先后顺序***：是下列的 ① -②/⑤ - ③ - ④

​	①默认初始化	②显式初始化（属性内直接赋值） ③构造器内初始化  ④ 通过”对象.方法“或”对象.属性“的方式赋值  

​	⑤在代码块中赋值

### 扩展知识

1. JavaBean是指符合以下标准的java类：类是公共的，有一个无参的公共的构造器，有属性且又对应的get、set方法

2. UML类图

![JavaBean](https://s4.ax1x.com/2022/02/23/bpatDP.png)

---

### 关键字的使用

#### this

1. this可以用来修饰、调用：属性、方法、构造器

2. this修饰属性和方法：此时this理解为当前对象 或 当前正在创建对象（构造器内中的说法）

   2.1在类的方法中，可以使用”this.属性“或”this.方法“的方式调用当前对象的属性和方法。在一般情况下，都选择省略”this.“。但是在特殊情况下，如果方法的形参和类的属性同名时，则必须显式的使用”this.变量“的方式，表明此变量是属性，而非形参。

​		2.2 在类的构造器中，可以使用”this.属性“或”this.方法“的方式调用当前正在创建的对象的属性和方法。在一般情况下，都选择省略”this.“。但是在特殊情况下， 如果构造器的形参和类的属性同名时，则必须显式的使用”this.变量“的方式，表明此变量是属性，而非形参。

3. this调用构造器

​	①在类的构造器中，可以显式的使用”this(形参列表)“方式，调用指定类的其他构造器

​	②构造器中不能通过”this(形参列表)“方式调用自己

​	③如果一个类中有n个构造器，则最多只有n-1	构造器使用“this（形参列表）”

​	④规定：“this（形参列表）”必须声明在构造器的首行

​	⑤构造器内部最多只有一个声明“this（形参列表）”，用来调用其他的构造器# 

##### package（包）	

1. 为了更好实现项目中类的管理，提供了包的概念。
2. 用package声明类和接口所属的包，声明在源文件首行
3. 包也属于标识符，也需要遵循标识符命名规则、规范（都要小写）
4. 每一个“.”代表一层文件目录。

补充：同一个包下不可以命名同名的接口和类，不同的包名可以

![MVC设计模式](https://s4.ax1x.com/2022/02/23/bpaw4g.png)

#### import（导入）

1. 在源文件中显式的使用import结构导入指定包下的类、接口
2. 声明在包的声明和类的声明之间。
3. 需要导入多个结构并列写出即可。
4. 也可以使用“XXX.*”的方式来表示导入xxx包下的所有结构
5. 使用的类和接口属于Java.lang包下定义的就可以省略import结构
6. 在同一个包下定义的类和接口也可以省略import结构
7. 如果在源文件中使用了不同包下的同名的类，则必须至少有一个类需要以全类名的方式显示

~~~java
//全类名的方式
com.java.exer3.Account acct1 = new com.atgugui.exer3.Account(1000);
~~~

8. 使用“xxx.*”方式调用xxx包下的所有结构。但是如果使用的是xxx子包下的结构，依然还是需要显式导入
9. import static 导入指定类或接口中的静态结构 

---

## 面向对象中

### 继承性

1. 继承性的好处：①减少代码的冗余，提高代码的复用性  ②便于功能的扩展  ③为多态性的使用提供前提
2. 继承性的格式： class A extends B{}

​	A 子类、派生类、subclass  B父类、超类、基类、superclass

3. 体现：一旦子类A继承父类B以后，子类A中就获取了父类B中声明所有的方法和属性

​	①父类中声明为private的属性或者方法，子类继承父类之后，仍然认为获取了父类中的私有结构，只是因为封装性的影响，使得子类并不能直接调用父类的结构。

​	②子类继续父类之后还能声明自己特有的属性和方法，实现功能的扩展。（子类的功能性更大一些）

其余补充：子类与父类的关系不同于子集和集合的关系。

![继承性](https://s4.ax1x.com/2022/02/23/bpacD0.png)

4. Java关于继承性的规范

​	①一个父类可以被多个子类继承

​	②java中的类的单继承性是一个类只能有一个父类

​	③子父类是相对的概念

​	④子类直接继承的父类称为直接父类，间接继承的父类成为间接父类

​	⑤子类继承父类以后就直接获取父类以及所有间接父类声明的属性和方法

5. 如果没有显式的声明一个类的父类，则此类继承于java.lang.Object类，所有的Java类（除了java.lang.Object类之外）都直接或间接继承于java.lang.Object类，也就意味着所有的Java类都具有java.lang.Object类声明的功能

---

### debug使用

1. 调试程序的方法 system out println（）
2. step over 执行完当前行的语句，进入下一行

​		step into 进入当前行所调用的方法

​		force step into 强制进入调用的方法

​		drop frame 返回当前方法调用处

​		step out	跳出该方法，返回到该方法被调用处的下一行语句

debug使用相关https://blog.csdn.net/u012757419/article/details/85294885

---

### 方法的重写

1. 重写(override/overwrite)：子类继承父类以后，对父类中同名同参数的方法进行覆盖操作
2. 重写以后，当创建子类对象以后，通过对象调用子父类中的同名同参数的方法时，调用的是子类中重写的父类的方法。
3. 方法重写的规定  

​			方法的声明 ： 权限修饰符 返回值类型 方法名（形参列表） throw 异常的类型{
​								方法体
​						}

子类中的方法称为重写的方法，父类中的方法称为被重写的方法

①子类重写的方法的方法名和形参列表一定是与父类中被重写的方法相同。

②子类重写的方法的权限修饰符不小于父类被重写的方法的权限修饰符 唯一一种特殊情况时子类不能重写父类中声明为private权限的方法

③返回值类型 

​		父类被重写的方法返回值类型是void，则子类中重写的方法返回值也只能 是void

​		父类被重写的方法返回值类型是A类，则子类中重写的方法返回值类型可以是A类也可以是A类的子类

​		父类被重写的方法返回值类型如果是基本数据类型（比如是double），那么子类重写的方法的返回值类型必须是相同的基本数据类型（必须也是double）

④子类中重写的方法抛出的异常类型不大于父类中被重写的方法抛出的异常类型

额外说明：子类和父类同名同参数的方法要么都声明为非static的（可以重写），要么都声明为static的（不能被重写）

---

### super关键字

1. super理解为父类的....

2. super可以用来修饰属性、方法、构造器

3. 可以在子类的方法或构造器中使用 ”super.属性“、”super.方法“显式的调用父类中声明的属性和方法。

   ①但通常情况下都省略super.的结构。但是面临特殊情况，如子类和父类定义了同名的属性，想在子类当中调用父类中声明同名的属性，则必须显式的使用super.属性的方法，表明调用的是父类当中的属性

   ②特殊情况，当子类重写了父类中的方法以后，要在子类的方法中调用父类被重写的方法时，必须显式的使用”super.方法“的方式表明调用的是父类中被重写的方法

4. super调用构造器

​		①可以在子类的构造器内显式的使用”super（形参列表）“	的方式，表明调用父类中的指定的构造器

​		②”super（形参列表）“	的方式必须声明在子类构造器的首行

​		③在类的构造器中，针对”this（形参列表）“ 或者 ”super（形参列表）“只能二选一，不能同时出现

​		④在构造器首行没有显式声明”this（形参列表）“ 或者 ”super（形参列表）”则默认调用父类中空参的构造器

​		⑤在类的多个构造器当中，至少有一个类的构造器使用了 ”super（形参列表）”，调用父类的构造器

---

### 子类对象的实例化过程

1. 从结果上看（体现了继承性）：子类继承父类以后就获取了父类中声明的属性和方法，而创建子类的对象，在堆空间中就会加载所有父类中声明的属性。
2. 从过程上看，通过子类的构造器创建子类对象时，一定会直接或间接调用父类的构造器，进而调用父类的父类的构造器，直至调用了java.lang.Object类中的空参构造器，因为加载过父类中的结构，内存中才显示了父类中的结构，子类对象才可以考虑调用
3. 虽然创建子类对象时调用了父类中的构造器，但始终还是只有一个对象，即new的这一个对象

---

### 多态性

​		理解多态性：一个事物的多种形态。

![多态的理解](https://s4.ax1x.com/2022/02/23/bpBka6.png)

  **多态是个运行时行为**，因为程序只能在程序运行的时候才能决定调用哪个对象的方法

~~~java
//示例
//等号右边new必须是等号左边的子类
Person p = new Man();
~~~



1. 对象多态性：父类的引用指向子类的对象（子类的对象赋给父类的引用）
2. 多态的使用：虚拟方法调用（有了对象以后多态性以后，在编译期间只能调用父类中声明的方法，但在运行期间实际执行的是子类重写父类的方法）

​		总结就是编译看左边，执行看右边

4. 多态性使用的前提 ① 类的继承关系  ②要有方法的重写
5. 对象的多态性只适用于方法，不适用于属性（属性编译运行都看左边）。
6. 有了对象的多态性以后，内存中实际上是加载了子类特有的属性和方法的，但是由于变量声明为父类类型，导致编译时，只能调用父类声明的属性和方法，子类特有的属性和方法不能调用。 
7. 子类继承父类

​	①若子类重写了父类方法，意味着子类里定义的方法彻底覆盖了父类里的同名方法，系统不可能将父类里的方法转移到子类中

​	②对于实例变量（类的属性）则不存在这样的现象，即使子类里面定义了与父类完全一样相同的实例变量，这个实例变量依然不可能覆	盖父类中定义的实例变量（类的属性）

补充：针对第6点想要调用子类特有的属性和方法

向下转型：使用强制类型转换符 Man m1 = （Man）p2   与基本数据类型强转相似  但使用强转时有可能出现ClassCastException异常

使用向下转型的目的是为了调用子类中特有的属性和方法

![类型转换](https://s4.ax1x.com/2022/02/23/bpaf5F.png)

---

### Instanceof关键字

1. a instanceof A：判断对象a是否为类A(其子类)的实例。如果是返回true，若不是返回false

   实际上是根据父类的引用来判断的，而不是根据此引用对象的类型来决定的 

   例如 Obeject obj = new Person();  判断相同与否看的是new Person()这个与instanceof后方的类是否是当前类或其子类的实例对象

2. 使用情景： 为了避免在向下转型时出现异常，在进行向下转型之前进行instanceof的判断，一旦返回true就执行向下转型，如果返回的是false，则不能执行

​		如果 a instanceof A 返回true，a  instanceof B也返回true，则类B是类A的父类

~~~java
//new的只能是同级类或者此类的子类。
//问题一：编译通过，运行不通过
//举例一
Person p3 = new Women();
Man m3 = (Man)p3;
//举例二
Person p4 = new Person();
Man m4 = (Man)p4;

//问题二：编译通过，运行通过
Object obj = new Woman();
Person p = (Person)obj;

//问题三，编译不通过
//等号右边new必须是等号左边的子类
Man m5 = new Women(); 
~~~

---

### Object类的使用

1. object类是所有java类的根父类
2. 如果在类的声明中没有显示的使用extends关键字指定其继承的父类，则默认父类为Java.lang.Object类
3. Object类中的属性和方法具有通用性
4. Object类有一个默认的空参构造器

finalize（）：对象被回收之前调用当前对象的finalize的方法通知系统进行回收，一般不主动调用，有垃圾回收器自动调用

引用数据类型分为了类 数组 接口，其中数组可以看作一个特殊的类，也继承了Object类，属于Object的子类

#### ==和equals的区别

1. ==的使用

==：运算符   

①可以使用在基本数据类型变量和引用数据类型变量当中

②如果比较的是基本数据类型变量，比较两个变量保存的数据是否相等，但不一定要求数据类型相同（i == d）

如果比较的是引用数据类型变量，比较两个对象的地址值是否相同

补充：== 符号使用时，必须保证符号左右两边的变量类型一致

2. equals（）方法的使用

①equals（）是个方法，而非运算符。

②只适用于引用数据类型

③Object类中equals()的定义：

~~~java
public boolean equals(Object obj) {
        return (this == obj);
    }
~~~

说明：Object类中定义的equals()和 == 作用相同，比较两个对象地址值是否相同

④**像String、Date、File、包装类等都重写了equals()方法，重写以后，比较的不是两个引用的地址是否相同，而是比较两个对象的“实体内容”是否相同**

3. 通常情况下，自定义的类如果使用equals()方法通常也是比较两个对象的“实体内容”是否相同，则需要对Object类中的equals()方法进行重写

重写的规则：比较两个对象的实体内容是否相同

特别需要注意的是在集成开发环境中自己生成和手动生成的区别是：自动生成的判断的是两个类的类型是否一样

自己写的判断的是两个类是否互为父子类，是则会返回true，但是又漏洞 例如父类和子类中都生成了同样的属性。

~~~java
Person p = new Person():
Man m = new Man();
//在自动生成中由于判断是两个类的类型是不同的，所以返回了false
//而在手动写的方法中instance判断了m是person的子类实例，返回了true，进入下一步强转，导致最终返回了true，这就是手动生成的弊端所在
system,out,println(p.age.equals(m));

~~~

![== 与 equals的区别](https://s4.ax1x.com/2022/02/23/bpaovR.png)

![方法原则](https://s4.ax1x.com/2022/02/23/bpaHDx.png)

---

### toString()方法

1. 输出一个对象的引用时，实际上就是调用当前对象的toString（）

~~~java
Customer cust = new Custeomer（"你好"）
    system.out.println(cust.toString());//地址值			//ObejctExer.MyDate@1540e19d
 	system.out.println(cust);//地址值=数据类型+@+地址
~~~

2. Obejct类中toString()的定义:

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

3. 像String、Date、File、包装类等都重写了Object类中的toString()方法，重写以后，调用该方法输出的是对象的”实体内容“信息
4. 自定义类也可以重写toString()方法，以返回对象实体内容

​	重写toString()方法

~~~java
public Stirng toString(){
    return "Customer[name = " + name + ",age = " + age + "]"
}
~~~

---

### 单元测试使用方法

1. 此时的java类要求：①类是public ②此类提供公共的无参的构造器
2. 此类声明单元测试的方法：此时的单元测试方法：方法的权限是public，没有返回值，没有形参
3. 此单元测试方法上需要声明@Test，并在单元测试中导入包import org.junit.Test;
4. 声明好单元测试方法以后，就可以在方法体内测试相关的代码
5. 完成后，左键双击单元测试方法名，右键：run 单元测试方法名（）
6. 执行结果绿色正常 红色异常

---

### 包装类的使用

1. java提供了8种基本数据类型对应的包装类

   包装类的目的是使得基本数据类型的变量具有类的特征

![包装类](https://s4.ax1x.com/2022/02/23/bpavPe.png)

2. 基本数据类型、包装类、String三者之间的转换

~~~java
//jdk5.0之前的转换法
//基本数据类型 --->> 包装类：调用包装类的构造器
	int num = 10；
    Integer in1 = new Integer(num)
    system.out.println(in1.toString());

	 Integer in2 = new Integer("123")
    system.out.println(in2.toString());

	Boolean b1 = new Boolean(true)；
    Boolean b1 = new Boolean("TrUe")；
    //忽略大小写的情况只要不为空值且也是true则返回true；
    system.out.println(b2);//true
	Boolean b1 = new Boolean("true122")
    system.out.println(b3); //false


//包装类 --->> 基本数据类型：调用包装类的xxxValue()
    Integer in1 = new Integer(12);
	int i1 = in1.intValue();
	system.out.println(i1);

//基本数据类型、包装类 --->> String类型
	//方式一：连接运算
	int num1 = 10；
        String str1 = num + "";
	//方式二：调用String的valueOf(Xxx xxx)
	float f1 = 13.1f；
    String.valueOf(f1);//"13.1"

~~~

3. JDK5.0新特性：自动装箱和自动拆箱

~~~java
//jdk5.0之后的转换法
//自动装箱：基本数据类型 ---> 包装类
	int num = 10；
    Integer in1 = num；//自动装箱
    booolean b1 = true；
    Boolean b2 = b1；//自动装箱
        
//自动拆箱：包装类 ---> 基本数据类型  
    int num3 = in1;//自动拆箱

//String类型 --->> 基本数据类型、包装类:调用包装类的parseXxx(String s)
	String str1 = "123";
	//可能会报NunberFormatException
	int num = Integer.parseInt(str1);
    system.out.println(num + 1);//其他类型同理

//基本数据类型、包装类 --->> String类型
//方式二：调用String的valueOf(Xxx xxx)
	float f1 = 13.1f；
    String.valueOf(f1);//"13.1"
 
~~~


~~~java
		//相关的面试题目
		Integer i = new Integer(1);
		Integer j = new Integer(1);
		System.out.println(i == j);//false
		
		//Integer内部定义了IntegerCache结构，IntegerCache中定义了Integer[],
		//保存了从-128~127范围的整数。如果我们使用自动装箱的方式，给Integer赋值的范围在
		//-128~127范围内时，可以直接使用数组中的元素，不用再去new了。目的：提高效率
		
		Integer m = 1;
		Integer n = 1;
		System.out.println(m == n);//true

		Integer x = 128;//相当于new了一个Integer对象
		Integer y = 128;//相当于new了一个Integer对象
		System.out.println(x == y);//false
	}
~~~

---

## 面向对象下

### static关键字

1. static：静态
2. static可以用来修饰类的内部结构：属性、方法、代码块、内部类
3. 使用static修饰属性：静态变量(类变量)

​		3.1属性：是否使用static修饰，又分为静态属性 和 非静态属性(实例变量)

​	实例变量：当创建了类的多个对象，每个对象都独立拥有自己的一套非静态属性，修改其中一个的非静态属性，不会导致其他对象中同样的属性值的修改

​	静态变量：当创建了类的多个对象，每个对象都使用同一个静态变量，当通过一个对象修改静态变量时，会导致其他对象调用此静态变量时，是修改过了的。

​		3.2 static修饰属性的其他说明

​			①静态变量随着类的加载而加载，可以通过"类.静态变量"的方式去调用

​			②静态变量的加载要早于对象的创建	

​			③由于类只加载一次，所以静态变量在内存中也只有一份：存在方法区的静态域中

​			④					类变量 	实例变量
​				类				yes				no
​				对象			yes				yes

![内存解析](https://s4.ax1x.com/2022/02/23/bpdCrt.png)

4. 使用static修饰方法：静态方法

①静态方法随着类的加载而加载，可以通过"类.静态方法"的方式去调用

②									静态方法	非静态方法
						类				yes				no
						对象			yes				yes

③静态方法中，只能调用静态方法或者属性

但在非静态方法中，既可以调用非静态的方法或属性，也可以调用静态方法或属性

5. static注意点

​		①在静态方法内，不能使用this和super关键字

​		②关于静态属性和静态方法的理解都从生命周期的出发

6. 开发中，如何确定一个属性是否要声明为static的？

​		>属性是可以被多个对象共享，不随着对象的不同而不同的

​		>类中的常量也常常声明为static

​		开发中，如何确定一个方法是否要声明为static的？

​		>操作静态属性的方法，通常设置为static的

​		>工具类中的方法，习惯上声明为static的，比如：Math、Array、Collections

---

### 单例设计模式

1. 对设计模式的理解：实际就相当于一个“套路"，针对不同的问题使用不同的“套路"

​	而所谓的单例设计模式就是指采用一定的方法保证整个软件系统当中某个类只有一个对象

2. 单例设计模式的实现 

​	2.1：饿汉式单例模式实现

```java
public class SingleTonTest {
    //1.私有化类的构造器 目的是为了避免外部调用构造器造对象
    private SingleTonTest(){
    }

    //2.内部创建类的对象
    //4.要求类的属性也必须是静态的
    private static SingleTonTest test = new SingleTonTest();

    //3.提供公共的静态方法返回类的对象
    public static SingleTonTest getTest(){
        return test;
    }
}
```

2.2：懒汉式单例模式实现

```java
class Test{
    //1.私有化类的构造器
    private Test(){

    }
    //2.声明当前类的对象，没有初始化
    //4.要求此对象也必须是静态的
    private static Test test1 = null;

    //3.声明public、static的返回当前类对象的方法
    public static Test getTest1(){
        if (test1 == null){
            test1 = new Test();
        }
        return test1;
    }
}
```

  线程安全的单例模式

~~~java
class SingleTonImprovement{
    //1.私有化构造器
    private Improvement(){

    }

    //2.创建类的对象，赋予空值
    //3.要求类的对象也必须是静态的
    private static Improvement im = null;

    //同步代码块   高效率的线程安全
    public static Improvement getIm(){
        //只有前几个线程进入到if的判断中，后面的线程会直接判断不为空，返回对象，因此效率较高
        if (im == null){
            synchronized(Improvement.class){
                if (im == null){
                    im = new Improvement();
                }
            }
        }
        return im;
    }
}
~~~

3. 区分 饿汉式 和 懒汉式

​	饿汉式：

​	坏处：对象加载时间过长 好处：是线程安全的

​	懒汉式：

​	好处：延迟对象的创建	目前写法的坏处：线程是不安全的

#### 单例设计模式应用场景

![单例设计模式](https://s4.ax1x.com/2022/02/23/bpdFVf.png)

---

### 关于main()方法的说明

1. main()方法作为程序的入口
2. main()方法也是个普通的静态方法
3. main()方法也可以作为与控制台交互的方式

---

### 代码块 (也称为初始化块)

格式

{

}

1. 代码块的作用：用来初始化类、对象
2. 只能使用static修饰代码块
3. 分类：静态代码块 和 非静态代码块
4. 静态代码块

​		①内部可以有输出语句

​		②随着类的加载而执行，而且只执行了一次

​		③作用：初始化类的信息

​		④如果一个类中定义了多个静态代码块，则按照声明的先后顺序执行

​		⑤静态代码块的执行优先于非静态代码块的执行

​		⑥静态代码块内只能调用静态的属性和方法，不能调用非静态的结构

5. 非静态代码块

​		①内部可以有输出语句

​		②随着对象的创建而执行

​		③每创建一个对象，就执行一次非静态代码块

​		④作用：可以在创建对象时，对对象的属性等进行初始化

​		⑤如果一个类中定义了多个非静态代码块，则按照声明的先后顺序执行

​		⑥非静态代码块内可以调用静态的属性和方法，也可以调用非静态的属性和方法

总结：由父及子，先静态后非静态，先代码块后构造器

---

### final关键字

​	final：最终的

1. final可以修饰的结构：类、方法、变量

2. final 用来修饰一个类：此类不能被其他类继承（比如String类、System类、StringBuffer类）

3. final 用来修饰方法：表明此方法不能再被重写（比如Object类中getClass()方法）

4. final 用来修饰变量：此时的“变量”就称为常量

   ① final修饰属性：可以考虑赋值的位置有：显式初始化、代码块内赋值、构造器内初始化

   多个对象使用的属性都一样考显式初始化，多个对象都有自己的一个属性值靠构造器内初始化，调用方法且有可能抛异常考虑再代码块内赋值

   ②final修饰局部变量：在使用final修饰局部变量时表明此变量是个常量，当在调用此方法时，给常量形参赋了一个实参，一旦赋值就只能在方法体使用此形参，但不能重新修改

static final用来修饰全局常量、全局方法

---

### 抽象类和抽象方法 abstract

1. abstract：抽象的
2. abstract可以修饰的结构： 类、方法
3. abstract修饰类：抽象类

​		①此类不能实例化

​		②抽象类中有构造器，便于子类实例化时调用

​		③开发中都会提供抽象类的子类，让子类实例化以完成相关操作

4. abstract修饰方法：抽象方法

~~~java
//抽象方法
public abstract void eat();
~~~

​				①抽象方法只有方法的声明，没有方法体

​				②包含抽象方法的类一定是个抽象类，反之抽象类里面不一定要有抽象方法的

​				③若子类重写了父类中的所有的抽象方法后，则子类才可以实例化

​					若子类没有重写了父类中的所有的抽象方法，则子类也是个抽象类，需要使用abstract修饰

5. abstract使用的注意点：

   ​    	①abstract不能使用来修饰：属性、构造器、代码块等结构

   ​		②不能用来修饰私有方法、静态方法、final的方法、final的类

---

#### 抽象类的匿名子类

~~~java
		Worker worker = new Worker();
		method1(worker);//非匿名的类非匿名的对象
		
		method1(new Worker());//非匿名的类匿名的对象
		System.out.println("********************");
		
		//创建了一个匿名子类的非匿名对象：p
		Person p = new Person(){
			@Override
			public void eat() {
				System.out.println("1");
			}
			@Override
			public void breath() {
				System.out.println("2");
			}	
		};

		method1(p);
		System.out.println("********************");
		//创建匿名子类的匿名对象
		method1(new Person(){
			@Override
			public void eat() {
				System.out.println("1");
			}

			@Override
			public void breath() {
				System.out.println("2");
			}
		});
	}
	public static void method1(Person p){
		p.eat();
		p.breath();
	}
~~~

---

### 模板方法的设计

~~~java
public class TemplateTest {
	public static void main(String[] args) {
		
		SubTemplate t = new SubTemplate();
		
		t.spendTime();
	}
}

abstract class Template{
	
	//计算某段代码执行所需要花费的时间
	public void spendTime(){
		
		long start = System.currentTimeMillis();
		
		this.code();//不确定的部分、易变的部分
		
		long end = System.currentTimeMillis();
		
		System.out.println("花费的时间为：" + (end - start));
		
	}
	
	public abstract void code();
	
	
}

class SubTemplate extends Template{

	@Override
	public void code() {
		
		for(int i = 2;i <= 1000;i++){
			boolean isFlag = true;
			for(int j = 2;j <= Math.sqrt(i);j++){
				
				if(i % j == 0){
					isFlag = false;
					break;
				}
			}
			if(isFlag){
				System.out.println(i);
			}
		}

	}
	
}
~~~

---

### 接口(Interface)

1. 接口使用interfac来定义

2. java中，接口和类是并列的

3. 如何定义接接口：定义接口中的成员

   ​	①JDK7及以前：只能定义全局常量和抽象方法

   ​		>全局常量：public static final的，但书写可以省略

   ​		>抽象方法：public abstract的

​			②JDK8：除了定义全局常量和抽象方法以外还可以定义静态方法(接口的静态方法只能此接口来调用，其实现类不能调用)、默认方法(接口的是实现了可以通		过实现类的对象调用)

~~~java
//如下的三个方法的权限修饰符都是public
    void methodAbstract();

    static void methodStatic(){
        System.out.println("我是接口中的静态方法");
    }

    default void methodDefault(){
        System.out.println("我是接口中的默认方法");

        methodPrivate();
    }
~~~

4. 接口中不能定义构造器，意味着接口不能实例化
5. 接口通过让类去实现（imlements）的方式来使用

​		①如果实现类覆盖了接口中所有的抽象方法，则此实现类才可以实例化对象

​		②如果实现类没有覆盖接口中所有的抽象方法，则此实现类也是个抽象类

6. Java类可以实现多个接口，----->弥补了Java单继承性的局限性

​		格式： class A extends B implements C,D

7. 接口与接口之间可以继承，且可以多继承
8. 接口的具体使用也体现了多态性
9. 接口，实际上可以看做是一种规范

#### 接口的应用 - 代理模式

~~~java
public class NetWorkTest {
	public static void main(String[] args) {
		Server server = new Server();
//		server.browse();
		ProxyServer proxyServer = new ProxyServer(server);
		
		proxyServer.browse();
		
	}
}
interface NetWork{
	
	public void browse();
	
}

//被代理类
class Server implements NetWork{

	@Override
	public void browse() {
		System.out.println("真实的服务器访问网络");
	}

}
//代理类
class ProxyServer implements NetWork{
	
	private NetWork work;
	
	public ProxyServer(NetWork work){
		this.work = work;
	}
	

	public void check(){
		System.out.println("联网之前的检查工作");
	}
	
	@Override
	public void browse() {
		check();
		
		work.browse();
		
	}
	
}
~~~

![接口的应用](https://s4.ax1x.com/2022/02/23/bpdAIS.png)

#### Java8中接口的新特性 

1. 接口定义的静态方法只能通过接口调用
2. 通过实现类的对象可以调用接口中定义的默认方法，如果实现类重写了默认方法，那么在调用的时候调用的是重写后的方法
3. **类优先原则**：如果子类（或实现类）继承的父类和实现的接口中声明了同名同参数的默认方法，那么子类在没有重写此方法的情况下，默认调用的是父类中同名同参数的方法
4. **接口冲突**：如果实现类实现了多个接口，而这多个接口中定义了同名同参数的默认方法，在没有重写此方法的情况下，编译器会报错，则必须在实现类中重写此方法
5. 如何在子类(或实现类)的方法中调用父类、接口中被重写的方法

~~~java
		public void myMethod(){
		method3();//调用自己定义的重写的方法
		super.method3();//调用的是父类中声明的
		//调用接口中的默认方法
        //接口名.super.方法名()
		CompareA.super.method3();
		CompareB.super.method3();
~~~

---

### 内部类

1. Java中允许将一个类A声明在另一个类B中，则类A就是内部类，类B称为外部类

2. 内部类的分类：成员内部类(静态、非静态) 和 局部内部类(方法内、代码块内、构造器内)

3. 成员内部类：

   ​	>一方面，作为外部类的成员：①调用外部类的结构

   ~~~java
   	Person.this.eat();//调用外部类的非静态属性
   ~~~

   ​	②可以用static修饰    ③可以被4种不同权限修饰

   ​	>另一方面，作为一个类：①类内可以定于属性、方法、构造器等 

   ​	②可以用final修饰，表示此类不可以被继承，不使用final则可以被继承   

   ​	③ 可以用abstract修饰，表示此类不能实例化

   4. 如何实例化内部类的对象

   ~~~java
   		//创建Dog实例(静态的成员内部类):
   		Person.Dog dog = new Person.Dog();
   		dog.show();
   		//创建Bird实例(非静态的成员内部类):
   		Person p = new Person();
   		Person.Bird bird = p.new Bird();
   		bird.sing();
   ~~~

   5. 如何在成员内部类中区分调用外部类的结构

   ~~~java
   public void display(String name){
   			System.out.println(name);//方法的形参
   			System.out.println(this.name);//内部类的属性
   			System.out.println(Person.this.name);//外部类的属性
   		}
   ~~~

   6. 开发当中局部内部类的使用

   ~~~java
   			//返回一个实现了Comparable接口的类的对象
   			public Comparable getComparable(){
   		
   			//创建一个实现了Comparable接口的类:局部内部类
   			//方式一：
   			class MyComparable implements Comparable{
   			@Override
   			public int compareTo(Object o) {
   				return 0;
   			}
   		}
   		return new MyComparable();
   ~~~

   注意点：①在局部内部类的方法中如果调用局部内部类所在的声明的方法中的局部变量，要求此变量必须是final的

   ②在jdk 7及之前，要求此局部变量显式声明为final的

   ​	在jdk 8及之后的版本，可以省略final的声明

    总结：成员内部类和局部内部类，在编译之后都会生成字节码文件

   格式：成员内部类：外部类$内部类名.class

​					局部内部类：外部类$数字 内部类名.class

---

## 异常

1. 定义：将程序运行中发生的不正常情况称为“异常”（但不包括语法错误和逻辑错误，这两者都不是异常）

   ~~~java
   		public static void main(String[] args){
   		//1.栈溢出：java.lang.StackOverflowError
   		//		main(args);
   		//2.堆溢出：java.lang.OutOfMemoryError 
   		Integer[] arr = new 				  		  Integer[1024*1024*1024];
   ~~~

2. 异常的体系结构

​		java.lang.Throwable

​					①java.lang.Error：一般不编写针对性代码处理

​					②java.lang.Exception：可以进行异常处理

​							----编译时异常（受检异常 check）

​									1.IOException

​									2.FileNotFoundException

​									3.ClassNotFoundException

​							----运行时异常（未检异常  uncheck RuntimeException）

​									1.NullPointerException

​									2.ArrayIndexOutOfBoundsException

​									3.ClassCastException(类型转换异常 向下转型)

​									4.NumberFormatException(数值转换异常)

​									5.InputMismatchException

​									6.ArithmeticException

3. 异常的处理：抓抛模型

过程一："抛"：程序在正常执行的过程中，一旦出现异常，就会在异常代码处生成一个对应异常类的对象。

并将此对象抛出。一旦抛出对象以后，其后的代码就不再执行。

关于异常对象的产生：① 系统自动生成的异常对象    ② 手动的生成一个异常对象，并抛出（throw）

过程二："抓"：理解为异常的处理方式：① try-catch-finally  ② throws

### try-catch-finally的使用

格式：

~~~java
try{
​			//可能出现异常的代码
}catch（异常类型1  异常变量名）{
​			//处理异常的方式1
}catch（异常类型2  异常变量名）{
​			//处理异常的方式2
}
...
finally{
​			//一定会执行的代码
}
~~~

说明：1.finally是可选的

2. 使用try将可能出现异常代码包装起来，在执行过程中，一旦出现异常，就会生成一个对应异常类的对象，根据此对象的类型，去catch中进行匹配

3. 一旦try中的异常对象匹配到某一个catch时，就进入catch中进行异常的处理。一旦处理完成，就跳出当前的try-catch结构（在没有写finally的情况），继续执行其后的代码

4. catch中的异常类型如果没有子父类关系，则谁在上面或者下面的位置并不重要。

   catch中的异常类型如果满足子父类关系，则要求子类一定声明在父类的上面。不然编译则报错

5. 常用的异常对象处理的方式： ① String  getMessage()    ② printStackTrace()

6. 在try结构中声明的变量，在出了try结构以后，就不能再被调用，除非在外面声明，在里面赋值

7. try-catch-finally结构可以相互嵌套

补充1：使用try-catch-finally处理编译时异常，只是让程序在编译时不再报错，但是运行时仍可能报错。相当于我们使用try-catch-finally将一个编译时可能出现的异常，延迟到了运行时出现。

体会2：开发中，由于运行时异常比较常见，所以通常就不针对运行时异常编写try-catch-finally了。但针对于编译时异常，则一定要考虑异常的处理。

---

#### try-catch-finally的finally的使用

1. finally中声明的是一定会比执行的语句，不管在catch中是否还会出现异常、try中有return语句、catch中有return语句还是照样执行
2. 像数据库连接、输入输出流、网络编程Socker等资源，JVM虚拟机是没办法进行回收的，要求自己进行手动的资源释放，那么就需要声明在finally结构中

补充：

final 用于声明属性，方法和类，分别表示属性不可变，方法不可覆盖，类不可继承。即如果一个类被声明为 final，意味着它不能作为父类被继承，因此一个类不能同时被声明为 abstract 的，又被声明为 final 的。变量或方法被声明为 final，可以保证它们在使用中不被修改。被声明为 final 的变量必须在声明时给赋予初值，而在以后的引用中只能读取，不可修改。被声明为 final 的方法也同样只能使用，不能重载。

finally 是异常处理语句结构的一部分，总是执行，常见的场景：释放一些资源，例如前面所说的 redis、db 等。在异常处理时提供 finally 块来执行任何清除操作，即在执行 catch 后会执行 finally 代码块。

finalize 是 Object 类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等。

##### finally 和 return的执行先后说明

①**finally 在 return 之后时，先执行 finally 后，再执行该 return，此时return返回的是其上行所得的数据值，而不是finally得到的数据值**

②**finally 内含有 return 时，直接执行其 return 后结束，此时return返回的finally得到的数据值，直接取得上方的return**

③finally 在 return 前，执行完 finally 后再执行 return。

### throws + 异常类型

1. “throws + 异常类型”声明在方法处（方法名后面），表示此方法执行时有可能抛出的异常。一旦方法执行后出现异常，就会在异常处生成一个对应异常类的对象，，此对象满足throws后异常类型，就会被抛出。而异常处之后的代码则不再执行和输出
2. try-catch-finally：真正的将异常处理了

​		throws的方式只是将异常抛给了方法的调用者，并没有将异常真正处理掉

3. 如何正确选择使用 try-catch-finally 还是 throws？

​		①如果父类中被重写的方法没有throws方式处理异常，则子类中重写的方法也不能使用throws，也就意味着如果子类中重写的方法有异常，则必须使用try-catch-finally的方式解决

​	②执行的方法a中，先后又调用了另外的几个方法，这几个方法是递进关系执行的。我们建议这几个方法使用throws的方式进行处理。而执行的方法a可以考虑使用try-catch-finally方式进行处理。

补充  方法重写的规则中曾经提到：子类中重写的方法抛出的异常类型不大于父类中被重写的方法抛出的异常类型。**是因为父类方法中抛出一个异常，子类继承重写方法后若异常类型大于父类异常类型，通过多态调用子类方法时，抛出的异常类型在父类中的try-catch-finally没办法解决。**

~~~java
public void regist(int id) throws Exception {
		if(id > 0){
			this.id = id;
		}else{
			//手动抛出异常对象
//			throw new RuntimeException("您输入的数据非法！");
//			throw new Exception("您输入的数据非法！");
			throw new MyException("不能输入负数");
		}
		
	}
~~~

### 自定义异常类

1. 继承于现有的异常结构：RuntimeException、Exception
2. 提供全局常量：serialVersionUID
3. 提供重载的构造器

~~~java
//自定义负数异常类
class EcDef extends Exception{
    static final long serialVersionUID = -7037193246939L;
    public EcDef(){

    }
    public EcDef(String str){
        super(str);
    }
}
~~~

