---
title: IDEA使用技巧笔记02
categories: IDEA使用技巧
date: 2022-06-04 17:00:00
typora-root-url: ../
---

### IDEA（windows）使用技巧笔记02

<font color="red">注：本博客整理的是windows下的IDEA相关的快捷键，MAC等系统可能有所差异。</font>

##### 1. 编写高质量代码

| 使用场景        | 快捷键                                                      | 说明             | 所属菜单 |
| --------------- | ----------------------------------------------------------- | :--------------- | -------- |
| **1. 代码重构** | shift + F6                                                  | 重构变量名       | Refactor |
|                 | ctrl  + F6<br />(更方便的方式：<br />修改后按 alt + Enter ) | 重构方法签名     | Refactor |
| **2. 抽取**     | ctrl + alt + V                                              | 抽取为变量       | Refactor |
|                 | ctrl + alt + C                                              | 抽取为静态变量   | Refactor |
|                 | ctrl + alt + F                                              | 抽取为成员变量   | Refactor |
|                 | ctrl + alt + P                                              | 抽取为方法参数   | Refactor |
|                 | ctrl + alt + M                                              | 抽取代码段为方法 | Refactor |

##### 2. 寻找修改轨迹（git的集成）

+ **annotate**：在代码行数上右键可以看到此选项，打开后能看到commit信息、作者等。

+ **revert**：撤销，对应快捷键**“ctrl + alt + Z”**，可以对：

  + 单处修改
  + 单个文件
  + 文件夹

  进行撤销操作，回滚到上次提交的状态。

+ **local history **: 本地修改记录，通过Help-> Find Action (或快捷键ctrl shift A) 搜索“local history”，打开此对话框，可以查看idea的本地的修改记录，即使未使用任何git等版本控制工具，也可以使用。

##### 3. 关联一切（待补充）

- ###### 与Spring的关联

- ###### 与数据库的关联

##### 4. 调试程序

| 使用场景        | 快捷键  | 说明             | 所属菜单 |
| --------------- | --------------- | ----------- | -------- |
| **1. 断点调试** | ctrl + F8| 当前行添加断点/删除断点 | Run |
|                | shift + F9 | 单步调试（Debug模式运行） | Run |
| | F8 | 单步运行（Step Over，Debug模式下） |  |
| | F9 | 运行到下一个断点（Resume，Debug模式下，<br />若没有下一个断点则运行至结束） |  |

