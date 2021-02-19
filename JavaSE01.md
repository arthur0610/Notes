# JavaSE 01
## 1.1 Java和PHP区别

## 1.2 正则表达式
Regular expression
使用单个字符串来描述并匹配一系列匹配某个句法规则的字符串

第一次使用正则表达式是在不经意间的

在学习`Linux`环境中，如何在文件夹中检索出包含类似字符的文件或者子文件夹

Case 1: 在`/home`目录下所有文件中查找包括test字符串的文件
```linux command
grep -r -e "test" /home/
```

`?`通配符匹配0个或1个字符

`*`通配符匹配0个或者多个字符

`+`通配符匹配1个或者多个字符

Case 2:
```
^[0-9]+abc$
```
`^`为匹配输入字符串开始位置

`$`为匹配输入字符串结束位置

`0-9+`匹配多个数字

`abc$`匹配字母`abc`并以`abc`作为结尾

Case 3:
```
^[a-z0-9_-]{3,15}$
```
`a-z0-9_-`匹配小写字母`a-z`和数字`0-9`和下划线`_`连字符`-`

Case 4: Java中支持正则表达式操作
```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

class RegExpTest{
    public static void main(String[] args) {
        String str = "";
        Pattern p = Pattern.complie(".*?(?=\\()");
        Matcher m = p.matcher(str);
        if(m.find()) {
            System.out.println(m.group());
        }
    }
}
```
Result:
```

```

## 1.3 Java和JavaScript

## 1.4 Java当中如何跳出多重循环
`break`是结束整个循环体
`continue`是结束单次循环

使外层循环条件表达式的结果可以受到里层循环代码的控制
```java
public static void main(String[] args) {
        System.out.println("标记前");
        boolean flag = true;
        for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 10 && flag; j++) {
                System.out.println("i=" + i + ",j=" + j);
                if (j == 5)
                    flag = false;
            }
        }
        System.out.println("标记后");
    }
```
Result:
```

```

## 1.5.1 & 和 &&
作为位运算符而言：

`&` 与运算 具有一下规则 (有0则为0)

```java
0&0=0;
0&1=0;
1&0=0;
1&1=1;
```

作为逻辑运算符而言：

`&` 不管左端条件是否正确，右端条件都执行

`&&`  只有当左端条件正确时，右端条件才执行

效率而言 `&&`更好

## 1.5.2 | 和 ||
作为位运算符而言：

`&` 或运算 具有一下规则 (有1则为1)

```java
0|0=0;
0|1=1;
1|0=1;
1|1=1;
```

## 1.6 int和Integer
Java是面向对象对象语言，但是还是引入了基本数据类型，为了让这些基本的数据类型也能被当成对象来操作，就引入了对应的包装类。`int`的包装类就是`Integer`。 Java从1.5版本引入了`自动装箱/拆箱`机制使二者可以相互转换。

Java的原始数据类型：`boolean, char, byte, short, int, long, float, double`

对应的包装类型：`Boolean, Character, Byte, Short, Integer, Long, Float, Double`

```java
class AutoUnboxingTest {
    public static void main(String[] args) {
        Integer a = new Integer(3);
        Integer b = 3;                  // 将3自动装箱成Integer类型
        int c = 3;
        System.out.println(a == b);     // false 两个引用没有引用同一对象
        System.out.println(a == c);     // true a自动拆箱成int类型再和c比较
    }
}
```
Result:
```java

```

## 1.7 字符编码


## 1.8 String和StringBuffer

## 1.9 String是最基本的数据类型么？
