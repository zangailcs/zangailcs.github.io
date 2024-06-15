---
title: Java集合框架笔记03--Map;Collections工具类
categories: Java集合框架
date: 2022-05-10 17:00:00
typora-root-url: ../
---

### Java集合框架笔记03

#### Map 体系

<div align=center>
<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/JavaCollectionNotes/02_Map.png" width="25%" style="center"/>`
</div>



+ ##### Map特点

  （1）存储任意键值对

  （2）键：无序、无下标、不允许重复（唯一）

  （3）值：无序、无下标、允许重复

+ ##### 遍历方法

  + keySet
  + entrySet （效率高于keySet）

	```java
    Map<String, String> map = new HashMap<>();
    // 添加元素
    map.put("cn", "中国");
    map.put("uk", "英国");
    map.put("usa", "美国");
    // map.put("cn", "zhongguo"); 会覆盖 “中国” 的值
	
    // 遍历--两种方式：（1） keySet （2）entrySet
    System.out.println("====使用 keySet 遍历====");
    for (String key: map.keySet()) {
        System.out.println(key + "-----------" + map.get(key));
    }
	
    System.out.println("====使用 entrySet 遍历====");
    for (Map.Entry<String, String> entry:  map.entrySet()) {
        System.out.println(entry.getKey() + "-----------" + entry.getValue());
    }
	```

#### Map 实现类

##### HashMap

+ ##### 属性

  |      | 属性                     | 默认值 | 描述                                                         |
  | :--- | ------------------------ | ------ | ------------------------------------------------------------ |
  | 1    | DEFAULT_INITIAL_CAPACITY | 16     | 默认初始容量                                                 |
  | 2    | DEFAULT_LOAD_FACTOR      | 0.75f  | 默认加载因子（达到容量的75%以后，进行扩容）                  |
  | 3    | TREEIFY_THRESHOLD        | 8      | 桶链表长度大于此值后，（且数组长度大于64---见<5>），将链表调整成树 |
  | 4    | UNTREEIFY_THRESHOLD      | 6      | 树的元素个数小于6时，调整回链表                              |
  | 5    | MIN_TREEIFY_CAPACITY     | 64     | <3>中树化的另一个条件                                        |

+ ##### 总结

  （1）HashMap刚创建时，table是null（节省空间），添加第一个元素时，table容量调整为16

  （2）当元素个数大于阈值（cap*0.75）时，会进行扩容，容量变为原来的2倍（2倍：减少调整元素的个数）

  （3）jdk1.8：
  - 当每个链表长度大于8，且数组元素个数大于64时，调整为红黑树，目的是提高执行效率
  
  - 当链表长度小于6时，调整成链表

  （4）jdk1.8以前，链表是头插入，jdk1.8以后是尾插入
  
  （5）线程不安全，运行效率快；允许用null作为key或value
  
##### Hashtable

- 线程安全，运行效率慢；不允许null作为key或value

##### Properties

- Hashtable的子类，要求key、value都是String。通常用于配置文件的读取。

##### TreeMap

- 实现了SortedMap接口，可以对key自动排序

#### Collections工具类

 包含很多操作集合的静态方法。

（1）sort、binarySearch、reverse、shuffle、copy等

#### 集合、数组相互转换

- 数组转集合后，得到的是受限集合，不能添加和删除

  ```java
  String[] names = {"张三", "李四", "王五"};
  List<String> nameList = Arrays.asList(names);
  // nameList.add("赵六");    // 报错：java.lang.UnsupportedOperationException
  System.out.println(nameList);
  ```

- 把基本类型的数组转成集合时，需要修改为包装类型。

  ```java
  int[] nums = {100, 200, 300, 400, 500};
  List<int[]> list = Arrays.asList(nums);     // 返回类型：数组 的列表
  Integer[] nums2 = {100, 200, 300, 400, 500};
  List<Integer> integers = Arrays.asList(nums2);    // 返回类型：包装类  Integer的列表
  ```

