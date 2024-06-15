---
title: Lambda与Stream笔记02--Stream API
categories: Lambda表达式;Stream API
date: 2022-05-14 17:00:00
typora-root-url: ../
---

### Lambda表达式与Stream笔记02--Stream API

#### Stream 基础知识

- ##### Stream的三个操作步骤：

  （1）创建Stream

  （2）中间操作

  （3）终止操作（终端操作）

- ##### 创建Stream的四种方式：

  ```java
  		// 1. 通过Collection 系列集合提供的 stream() 或 parallelStream()
          List<String> list = new ArrayList<>();
          Stream<String> stream1 = list.stream();
  
          // 2. 通过Arrays中的静态方法 stream() 获取数组流
          String[] strings = new String[10];
          Stream<String> stream2 = Arrays.stream(strings);
  
          // 3. 通过Stream中的静态方法 of()
          Stream<String> stream3 = Stream.of("aa", "bb", "cc");
  
          // 4. 创建无限流
          // (1) 迭代
          Stream<Integer> stream41 = Stream.iterate(0, (x) -> x + 2);
  
          // (2) 生成
          Stream<Double> stream42 = Stream.generate(Math::random);
  ```

- ##### Stream 的中间操作

  - ###### 惰性求值

    多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何的处理，而在终止操作时一次性全部处理，称为“惰性求值”。

    ```java
    		// 中间操作： 不会执行任何操作
            Stream<Employee> stream = employees.stream()
                    .filter((e) -> {
                        System.out.println("Stream API 的中间操作");
                        return e.getAge() > 35;
                    });
    
            // 终止操作： 一次性执行全部内容，即 “惰性求值”
            stream.forEach(System.out::println); 	// 若注释此行，不会有任何输出
    ```

  - ###### 筛选与切片

    | 操作     | 说明                                              |
    | -------- | ------------------------------------------------- |
    | fliter   | 接收 Lambda ，从流中排除某些元素                  |
    | limit    | 截断流，使元素不超过给定数量                      |
    | skip(n)  | 跳过元素，返回一个扔掉了前n个元素的流             |
    | distinct | 筛选，通过元素的hashcode() 和 equals 去除重复元素 |

  - ###### 映射

    | 操作    | 说明                                                         |
    | ------- | ------------------------------------------------------------ |
    | map     | 接收lambda，将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。 |
    | flatMap | 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有的流连接成一个流。 |

    ```java
    // map
    List<String> list = Arrays.asList("aaa", "bbb", "ccc", "ddd");
    list.stream()
        .map(String::toUpperCase)
        .forEach(System.out::println);
    ```

  - ###### 排序

    | 操作                   | 说明     |
    | ---------------------- | -------- |
    | sorted                 | 自然排序 |
    | sorted(Comparator com) | 定制排序 |

- ##### Stream 的终止操作

  - ###### 查找与匹配

    | 操作                | 说明                     | 返回值      |
    | ------------------- | ------------------------ | ----------- |
    | allMatch            | 检查是否匹配所有元素     | boolean     |
    | anyMatch            | 检查是否匹配至少一个元素 | boolean     |
    | noneMatch           | 检查是否没有匹配所有元素 | boolean     |
    | findFirst           | 返回第一个元素           | Optional<T> |
    | findAny             | 返回当前流中的任意元素   | Optional<T> |
    | count               | 返回流中元素的个数       | Long        |
    | max(Comparator com) | 返回流中最大值           | Optional<T> |
    | min(Comparator com) | 返回流中最小值           | Optional<T> |

    -- Optional容器：操作结果可能为空时，返回Optional容器，解决空指针异常

  - ###### 规约

    - reduce(T identity, BinaryOperator bo)          --------------- 不可能为空，因为有初始值 identity， 所以返回 T

      ```java
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
      Integer sum = list.stream()
                        .reduce(0, Integer::sum);
      System.out.println(sum);
      ```

    - reduce(BinaryOperator bo)           -------------- 可能为空，返回Optional

      ```java
      Optional<Integer> op = list.stream()
                                 .reduce(Integer::sum);
      System.out.println(op.get());
      ```

  - ###### 收集

    - collect -- 将流转换成其他形式。接收一个Collector接口的实现，用于给流中的元素做汇总的方法。

      ​		     --可以使用 Collectors 工具类中提供的诸多静态方法获取已实现的收集器。

      Collectors 中常用的收集器：

      toList 、toSet、groupingBy（分组）

- 并行流与顺序流

  ​	调用 parallel() 方法或 sequential() 切换并行流 或 串行流