# Java基本程序结构
## 数据类型
* Java的数字字面量可以加下划线，如1_000_000
* Java没有任何无符号形式的int、long、short或byte类型
* float类型有一个后缀F或f
* Double.isNaN(x) // check whether x is "not a number"

## 字符串
* char charAt(int index) 返回指定位置的代码单元
* boolean equals(Object other) 判断字符串是否相等

## 输入和输出
* java.util.Scanner
* Scanner(InputStream in) 用给定的输入流创建一个Scanner对象
* String nextLine() 读取输入的下一行内容
* String next() 读取输入的下一个单词（以空格作为分隔符）
* int nextInt() double nextDouble() 读取或转换下一个表示整数或浮点数的字符序列
* boolean hasNext() 检测输入中是否还有其他单词
* boolean hasNextInt() boolean hasNextDouble() 检测是否还有下一个表示整数或浮点数的字符序列

# 对象和类
## 静态方法
* 方法不需要访问对象状态，因为它需要的所有参数都通过显示参数提供
* 方法只需要访问类的静态字段

## 方法参数
* 方法不能修改基本数据类型的参数（即数值型或布尔型）
* 方法可以改变对象参数的状态
* 方法不能让一个对象参数引用一个新的对象

# 继承
## equals()方法的建议
1. 显式参数命名为otherObject，稍后将它强制转化为一个名叫other的变量
2. 检测this与ohterObject是否相等：
```Java
if(this == otherObject)
    return true;
```
3. 检测otherObject是否为null，如果为null，返回false。这项检测是很有必要的。
```Java
if(otherObject == null)
    return false;
```
4. 比较this与otherObject的类。如果equals的语义可以在子类中改变，就使用getClass检测：
```Java
if(getClass()!==otherObject.getClass())
    return false;
```
5. 将otherObject强制转化为相应类类型的变量：
```Java
ClassName other = (ClassName) otherObject;
```
6. 现在根据相等性概念的要求来比较字段。使用==比较基本字段，使用Objects.equals()比较对象字段。如果所有字段都匹配，就返回true；否则就返回false。
```Java
return field1==other.field1
  && Object.equals(field2, other.field2)
  && ...;
```
如果在子类中重新定义equals，就要在其中包含一个super.equals(other)调用。

# 接口、lambda表达式和内部类
## 默认方法
* 可以为接口方法提供一个默认实现，用default修饰符进行修饰

## 默认方法冲突
* 超类优先：超类提供一个默认方法，相同签名的默认方法会被忽略
* 接口冲突：如果有一个接口进行了实现，程序员必须覆盖这个默认方法，否则存在二义性问题
* 类优先：类有默认方法，接口也有，则接口的默认方法被忽略
