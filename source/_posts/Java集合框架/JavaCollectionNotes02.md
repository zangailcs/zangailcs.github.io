---
title: Java集合框架笔记02--泛型;Set
categories: Java集合框架
date: 2022-05-05 17:00:00
typora-root-url: ../
---

### Java集合框架笔记02

#### 泛型

+ ##### 基本概念

  + 本质：参数化类型，把类型作为参数传递

  + 常见形式：泛型类、泛型接口、泛型方法

  + 语法：

    ```java
    <T, ...>     // T称为类型占位符，表示一种引用类型
    ```

  + 好处：

    （1）提高代码的重用性

    （2）防止类型转换异常，提高代码的安全性
    
  + 注意点：

    （1）泛型只能 使用引用类型

    （2）不同泛型对象之间不能相互赋值
  
+ ##### 泛型类的继承

  ```java
  class Father<T1, T2> {
  }
  
  // 子类不保留父类的泛型
  // （1）没有类型  擦除
  class Son<A, B> extends Father { 
      // 等价于  class Son extends Father<Object, Object>
  }
  
  // （2）具体类型
  class Son2<A, B> extends Father<Integer, String> {
  }
  
  // 子类保留父类的泛型
  // （1）全部保留
  class Son3<T1, T2, A, B> extends Father<T1, T2>{
  }
  
  // （2）部分保留
  class Son4<T2, A, B> extends Father<Integer, T2>{
  }
  ```

- ##### 泛型在继承方面的体现

  - 类A是类B的父类，但G<A>和G<B> 二者不具备子父类关系，二者是并列关系
    - G<A>和G<B>二者共同的父类是G<?> --------- ?为通配符
  - 类（接口）A是类B的父类（接口），A<G>是B<G>的父类

  	```java
  	AbstractList<String> list1 = null;
  	List<String> list2 = null;
  	ArrayList<String> list3 = null;
  	list1 = list3;      // 允许
  	list2 = list3;      // 允许
  	```

  - 使用通配符后的访问要求

    ```java
    List<String> list1 = new ArrayList<>();
    list1.add("AA");
    list1.add("BB");
    list1.add("CC");
    
    List<?> list2 = list1;
    
    // 添加（写入）：对于List<?>不能向其内部添加数据
    // 除了添加null之外。
    //list2.add("AA");       // 编译报错
    list2.add(null);         // 允许
    
    // 读取：允许读取数据，读取的数据类型为Object
    Object o = list2.get(0);
    System.out.println(o);
    ```

  - 有限制条件的通配符的使用

    - ?   ： （-∞, +∞）
    - ? extends Father: （-∞, Father]
    - ? super Father  :  [Father, +∞）

    ```java
    import java.util.List;
    
    class Father {
    }
    
    class Student extends Father {
    }
    
    public class Demo4 {
        public static void main(String[] args) {
            /*
            ?   ： （-∞, +∞）
            ? extends Father: （-∞, Father]
            ? super Father  :  [Father, +∞）
             */
    
            List<? extends Father> list1 = null;
            List<? super Father> list2 = null;
    
            List<Student> list3 = null;
            List<Father> list4 = null;
            List<Object> list5 = null;
    
            list1 = list3;
            list1 = list4;
            // list1 = list5;   //不允许
    
            // list2 = list3;   //不允许
            list2 = list4;
            list2 = list5;
        }
    }
    ```

#### 泛型集合

- ##### 基本概念

  - 参数化类型、类型安全的集合，强制集合元素的类型必须一致。
  - 特点：
    - 编译时即可检查，而非运行时抛出异常。
    - 访问时，不必类型转换。
    - 不同泛型之间引用不能相互赋值，泛型不存在多态。


#### Set

- ##### 基本概念

  - 特点：无序、无下标、元素不可重复
  - 方法：全部继承自Collection中的方法

- ##### 遍历方式

  - 增强for
  - 迭代器

#### Set 的实现类

- ##### HashSet

  - 基于HashCode计算元素的存放位置
  - 当存入元素的哈希码相同时，调用equals确认，如果结果为true，拒绝后者存入
  - 存储结构：哈希表（数组+链表+红黑树）

- ##### TreeSet

  - 基于排列顺序实现元素不重复
  - 实现了SortedSet接口，对集合元素自动排序
  - 元素对象的类型必须实现Comparable接口，指定排序的规则
    - 通过该接口中的CompareTo方法确定是否为重复元素
  - 存储结构：红黑树

