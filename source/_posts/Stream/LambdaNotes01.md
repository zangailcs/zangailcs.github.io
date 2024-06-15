---
title: Lambda与Stream笔记01--Lambda基础语法
categories: Lambda表达式;Stream API
date: 2022-05-15 17:00:00
typora-root-url: ../
---

### Lambda表达式与Stream笔记01--Lambda基础语法

#### 初体验

```java
    List<Employee> employees = Arrays.asList(
        new Employee("张三", 20, 3333.33f),
        new Employee("李四", 30, 6666.33f),
        new Employee("王五", 40, 2222.33f),
        new Employee("赵六", 35, 5500.33f),
        new Employee("田七", 50, 7000.33f)
    );

    public List<Employee> filterEmployee(List<Employee> employees, MyPredicate<Employee> myPredicate) {
        List<Employee> list = new ArrayList<>();
        for (Employee e: employees) {
            if (myPredicate.test(e)) {
                list.add(e);
            }
        }
        return list;
    }

    // 匿名内部类
    @Test
    public void test01() {
        List<Employee> list = filterEmployee(employees, new MyPredicate<Employee>() {
            @Override
            public boolean test(Employee employee) {
                return employee.getSalary() >= 5000;
            }
        });
        list.forEach(System.out::println);
    }

    // Lambda表达式
    @Test
    public void test02() {
        List<Employee> list = filterEmployee(employees, (e) -> e.getSalary() >= 5000);
        list.forEach(System.out::println);
    }

    // Stream API
    @Test
    public void test03() {
        employees.stream()
                .filter((e) -> e.getSalary() >= 5000)
//                .limit(2)       // 限制输出条数
//                .map(Employee::getName)         // 只输出名字
                .forEach(System.out::println);
    }
```

#### 基础语法

- ##### 箭头操作符(Lambda操作符) ->

  - 左侧：Lambda 表达式的参数列表
  - 右侧：Lambda 表达式中所需执行的功能，即Lambda 体

- ##### 语法格式

  1. 无参数，无返回值：

     ```java
     // () -> states;
     // eg：
     Runnable r = () -> System.out.println("Hello Lambda");
     ```

  2. 有一个参数，无返回值：

     ```java
     // eg:
     Consumer<String> con = (x) -> System.out.println(x);
     // or (只有一个参数时，可以省略小括号，但还是推荐写上)
     Consumer<String> con1 = x -> System.out.println(x);
     ```

  3. 有两个以上参数，有返回值，且Lambda体中有多条语句：

     ```java
     // 需要用大括号将Lambda体括起来
     Comparator<Integer> com = (x, y) -> {
     	System.out.println("函数式接口");
     	return Integer.compare(x, y);
     };
     ```

  4.  Lambda体中只有一条语句，且有返回值：return和大括号都可以省略：

     ```java
     Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
     ```

  5. Lambda表达式的参数列表的数据类型可以省略不写，因为JVM编译器可以根据上下文推断出数据类型（“类型推断”）。

- ##### Lambda表达式需要“函数式接口”的支持

  - 函数式接口：接口中只有一个抽象方法时，称为函数式接口。
    - 可以使用注解 @FunctionalInterface 修饰，以检查是否是函数式接口

#### Java8 内置的四大核心函数式接口

- Consumer<T> ： 消费型接口

  ```java
  void accept(T t);
  ```

- Supplier<T> ：供给型接口

  ```java
  T get();
  ```

- Function<T, R> ： 函数型接口

  ```java
  R apply(T t);
  ```

- Predicate<T> ： 断言型接口

  ```java
  boolean test(T t);
  ```


#### 方法引用

​		若Lambda 体中的内容有方法已经实现了，可以使用“方法引用”

- ##### 三种语法格式：

  - 对象::实例方法名

    ```java
    Consumer<String> con = System.out::println;
    ```

  - 类::静态方法名

    ```java
    Comparator<Integer> com = Integer::compare;
    ```

  - 类::实例方法名

    ```java
    BiPredicate<String, String> bp = String::equals;
    ```

- ##### 注意事项：

  - Lambda 体中调用方法的参数列表与返回值类型，要与函数式接口中抽象方法的保持一致
  - 若Lambda 参数列表中的第一个参数是实例方法的调用者，第二个参数是该实例方法的参数时，可以使用语法格式 类::实例方法名

#### 构造器引用

- ##### 语法格式：

  ```java
  ClassName::new
  
  // eg: (调用的是无参构造函数)
  Supplier<String> sup = String::new;
  
  // 调用有参构造函数： 借助 Function接口
  Function<String, String> fun = String::new;
  ```

  
