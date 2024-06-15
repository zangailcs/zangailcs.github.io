---
title: IDEA使用技巧笔记01
categories: IDEA使用技巧
date: 2022-06-01 17:00:00
typora-root-url: ../
---

### IDEA（windows）使用技巧笔记01

<font color="red">注：本博客整理的是windows下的IDEA相关的快捷键，MAC等系统可能有所差异。</font>

##### 1. 窗口跳转

（1）快捷键：**alt + 数字**

（2）可以设置显示窗口编号：

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/IDEA/01_show_tools_numbers.png" width="80%" style="center"/>

​          设置后，各工具窗口会显示其对应的数字编号，想跳转到某个窗口时，就可以按下“alt+相应的数字键”。

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/IDEA/02_tools_windows_ex.png" width="8%" style="center"/>

##### 2. 跳转相关的快捷键

| 使用场景 | 快捷键 | 说明           | 所属菜单 |
| ------ | ------ | :------------- | ------ |
| **1. 项目之间跳转** | ctrl + alt + ] | 跳转到下个项目 | Window |
|  | ctrl + alt + [ | 跳转到上个项目 | Window |
| **2. 文件之间跳转** | ctrl + E | 打开最近查看的文件（Recent Files） | View |
|  | ctrl + shift + E | 打开最近编辑的文件（Recently Changed Files） | View |
|  | ctrl + shift + Backspace<br />（**or鼠标侧键**） | 跳转到上次编辑的地方（Last Edit Location） | Navigate |
|  | <br />（**or鼠标侧键**） | 跳转到下次编辑的地方（Next Edit Location） | Navigate |
|  | （ctrl+）alt + 左键头 | 跳转到下次浏览的地方（Forward） | Navigate |
|  | （ctrl+）alt + 右键头 | 跳转到上次浏览的地方（Back） | Navigate |
| 利用标签跳转<br />浏览源码等场景 | F11 | 添加/删除书签 |  |
|  | ctrl + F11 | 添加带助记符号的书签 |  |
|  | ctrl + 数字键 | 带助记符号的标签之间的跳转 |  |
| 收藏位置和文件 | 双击Favourites 窗口<br />中的书签列表<br /><img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/IDEA/03_bookmarks.png" width="50%" style="center"/> | 也可以跳转到书签对应的代码位置 |  |
| **3.  编辑区和文件区<br />之间的跳转** | alt + 1 | 代码编辑区跳转到文件区 |  |
|  | Esc | 文件区跳转到代码编辑区 |  |

##### 3. 快速定位代码
| 使用场景 | 快捷键 | 说明           | 所属菜单 |
| ------ | ------ | :------------- | ------ |
| **精准搜索** | ctrl + N | 定位类 | Navigate |
|  | ctrl + shift + N | 定位文件 | Navigate |
|  | ctrl + alt + shift + N | 定位符号<br />（符号：方法名/属性） | Navigate |
|  | ctrl + shift + F<br />(若无效，可能是跟windows<br />自带的输入法的简繁体<br />切换快捷键冲突，关闭之。) | 定位字符串。<br />相关选项：<br />Cc: 区分大小写<br />W: 单词匹配<br />.*：正则表达式匹配<br />File mask: 在指定的文件类型中查找 | Edit |

##### 4. 代码小助手
| 使用场景 | 快捷键 | 说明           | 备注 |
| ------ | ------ | :------------- | ------ |
| **1. 移动光标与选择** | shift + 右箭头         | 选中下一个字符                                               | *小结：大部分移动光标的快捷键，<br />加上shift可以进行选中* |
|  | ctrl + 右箭头          | 移动光标到单词结束位置                                       |  |
| | ctrl + shift + 右箭头  | 选中到单词结束位置                                           |  |
| | Home                   | 移动光标到行首                                               |  |
| | ctrl + alt + shift + J | 批量选中操作<br />需要先选中一个符号/变量                    |  |
| **2. 代码模板**<br />（非快捷键） | live templates         | 可以自定义一些<br />类似psvm的代码模板，<br />减少敲重复代码 |  |
| | postfix completion     | 常用：<br />- for 循环<br />- nn 判断非空<br />- return 返回<br />- field 创建类中的属性 |  |
| **3. 万能神器** | alt + Enter            | 遇事不决，alt Enter                                          |  |
