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

## 构造器引用
```Java
Person[] people = stream.toArray(Person::new);
```

# 泛型程序设计
## 泛型方法
```Java
class ArrayAlg {
  public static <T> getMiddle(T... a) {
    return a[a.length / 2];
  }
}
```

## 泛型代码和虚拟机
* 虚拟机中没有泛型，只有普通的类和方法
* 所有的类型参数都会替换为它们的限定类型
* 会合成桥方法来保持多态
* 为保持类型安全性，必要时会插入强制类型转换

## 限制和局限性
* 不能用基本类型实例化类型参数
* 运行时类型查询只适用于原始类型
* 不能创建参数化的类型数组，要使用```ArrayList:ArrayList<Pair<String>>```收集参数化类型对象
* 不能实例化类型变量
* 不能构造泛型数组
* 泛型类的静态上下文中类型变量无效
* 不能抛出或捕获泛型类的实例
* 擦除后的冲突
```Java
class Employee implements Comparable<Employee> {...}
class Manager extends Employee implements Comparable<Manager> {...} // ERROR

class Employee implements Comparable {...}
class Manager extends Employee implements Comparable {...} // Correct
```

## 通配符类型
* ```Pair<Manager>```和```Pair<Employee>```没有关系
* ```Pair<Manager>```是```Pair<? extends Employee>```的子类型，但是只能读取泛型对象
* ```Pair<Manager>```是```Pair<? super Manager>```的子类型，但是只能写入泛型对象

# 集合
## 迭代器
```Java
it.remove();
it.remove(); // ERROR

it.remove();
it.next();
it.remove(); // OK
```

## 具体集合
* Vector类是同步的，ArrayList不是同步的。因此，在不需要同步时用ArrayList，同步时用Vector

## 算法
```Java
String[] values = staff.toArray(new String[0]);
String[] values = staff.toArray(new String[staff.size()]);
```

## 遗留的集合
* Hashtable类和HashMap类作用一样
* Hashtable类的方法也是同步的，如果需要并发，请使用ConcurrentHashMap

# 并发
## 什么是线程
```Java
Runnable r = ()->{ task code }; // 函数式接口
var t = new Thread(r);
t.start();
```

## 线程状态
* 一个可运行的线程，既可能在运行，也可能没有运行

## 线程属性
* interupted()方法是静态方法，检查当前线程是否被中断，**会清除当前线程的中断状态**
* isInterupted()方法是实例方法，检查是否有线程被中断，**不会改变中断状态**

## 同步
* 要确保临界区的代码不要因为抛出异常而跳出临界区。如果临界区代码结束之前抛出了异常，finally子句将释放锁，但是对象可能处于被破坏的状态
* 条件对象
```Java
sufficientFunds = bankLock.newCondition();

sufficientFunds.await();
sufficientFunds.signalAll();
```

* 锁用来保护代码片段，以此只能有一个线程执行被保护的代码
* 锁可以管理试图进入被保护代码段的线程
* 一个锁可以有一个或多个相关联的条件对象
* 每个条件对象管理那些已经进入被保护代码段但还不能运行的线程

* 最好既不使用Lock/Condition也不使用synchronized关键字。在很多情况下，可以使用```java.util.concurrent```包中的某些机制。
* 如果synchronized关键字适合你的程序，那么尽量使用这种做法
* 如果特别需要Lock/Condition结构提供额外的能力，则使用Lock/Condition

* volatile关键字为实例字段的同步访问提供了一个免锁机制，但不提供原子性

```Java
// 线程局部变量
public static final ThreadLocal<SimpleDateFormat> dateFormat = ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-mm-dd"));

String dataStamp = dateFormat.get().format(new Date());
```

## 任务和线程池
* 调用```Executors```类的静态方法```newCachedThreadPool```或```newFixedThreadPool```
* 调用```submit```提交```Runnable```或```Callable```对象
* 保存好返回的```Future```对象，以便得到结果或者取消任务
* 当不想再提交任何任务时，调用```shutdown```
