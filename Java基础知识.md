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
