> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/cea930923f3d)

在接触 springboot 的时候遇到了一种特殊的配置文件 .yml，本文对 yml 作简单介绍，快速入手 yml。

### 一、YML 是什么

YAML (YAML Ain't a Markup Language)YAML 不是一种标记语言，通常以. yml 为后缀的文件，是一种直观的能够被电脑识别的数据序列化格式，并且容易被人类阅读，容易和脚本语言交互的，可以被支持 YAML 库的不同的编程语言程序导入，一种专门用来写配置文件的语言。可用于如： Java，C/C++, Ruby, Python, Perl, C#, PHP 等。

### 二、YML 的优点

1.  YAML 易于人们阅读。
2.  YAML 数据在编程语言之间是可移植的。
3.  YAML 匹配敏捷语言的本机数据结构。
4.  YAML 具有一致的模型来支持通用工具。
5.  YAML 支持单程处理。
6.  YAML 具有表现力和可扩展性。
7.  YAML 易于实现和使用。

### 三、YML 语法

#### 1. 约定

*   k: v 表示键值对关系，冒号后面必须有一个空格
    
*   使用空格的缩进表示层级关系，空格数目不重要，只要是左对齐的一列数据，都是同一个层级的
    
*   大小写敏感
    
*   缩进时不允许使用 Tab 键，只允许使用空格。
    
*   松散表示，java 中对于驼峰命名法，可用原名或使用 - 代替驼峰，如 java 中的 lastName 属性, 在 yml 中使用 lastName 或 last-name 都可正确映射。
    

#### 2. 键值关系

(以 java 语言为例，其它语言类似) 对于键与值主要是看能否表示以下内容。普通的值 (数字、字符串、布尔)、日期、对象、数组、集合等。

##### 1) 普通值 (字面量)

k: v：字面量直接写；

​ 字符串默认不用加上单引号或者双绰号；

​ "": 双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

​ name: "zhangsan \n lisi"：输出；zhangsan 换行 lisi

​ ''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

```
name1: zhangsan
name2: 'zhangsan \n lisi'
name3: "zhangsan \n lisi"
age: 18
flag: true
```

##### 2) 日期

```
date: 2019/01/01
```

##### 3) 对象 (属性和值)、Map(键值对)

​ 在下一行来写对象的属性和值的关系，注意缩进

```
people:
    name: zhangsan
    age: 20
```

​ 行内写法:

```
people: {name:zhangsan,age: 20}
```

##### 4) 数组、list、set

用 - 值表示数组中的一个元素

```
pets:
    - dog
    - pig
    - cat
```

行内写法

```
pets: [dog,pig,cat]
```

##### 5) 数组对象、list 对象、set 对象

```
peoples:
    - name: zhangsan
      age: 22
    - name: lisi
      age: 20
    - {name: wangwu,age: 18}
```

##### 6)java 代码示例

java 代码 (省略 get,set 方法)

```
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;

    private Date birth;
    private Map<String,Object> maps;
    private List<Dog> lists;
    private Dog dog;
    private String[] arr;
｝
public class Dog {
    private String name;
    private Integer age;
}
```

对应的 yml

```
person:
  boss: false
  maps:
    k1: v1
    k2: 14
  lists:
    - name: d1
      age: 2
    - name: d2
      age: 3
    - {name: d3,age: 4}
  birth: 2017/12/15
  dog:
    name: p_dog
    age: 15
  age: 13
  last-name: 张三
  arr: [s1,s2,s3]
```

#### 3. 文档块

对于测试环境，预生产环境，生产环境可以使用不同的配置，如果只想写到一个文件中，yml 与是支持的, 每个块用 ---- 隔开

```
server:
  port: 8081
spring:
  profiles:
    active: prod #激活对应的文档块

---
server:
  port: 8083
spring:
  profiles: dev #指定属于哪个环境


---

server:
  port: 8084
spring:
  profiles: prod  #指定属于哪个环境
```