---
title: 初识 Swing
date: 2020-06-26 23:36:05
categories:
 - java
 - Swing
tags:
 - java
 - Swing
cover: https://s1.ax1x.com/2020/06/26/NypXtO.png
---

## 初识 Swing

### 什么是Swing?

Swing 是一个为Java设计的GUI工具包。

Swing是JAVA基础类的一部分。

Swing包括了图形用户界面（GUI）器件如：文本框，按钮，分隔窗格和表。

Swing提供许多比AWT更好的屏幕显示元素。它们用纯Java写成，所以同Java本身一样可以跨平台运行，这一点不像AWT。它们是JFC的一部分。它们支持可更换的面板和主题（各种操作系统默认的特有主题），然而不是真的使用原生平台提供的设备，而是仅仅在表面上模仿它们。这意味着你可以在任意平台上使用JAVA支持的任意面板。轻量级组件的缺点则是执行速度较慢，优点就是可以在所有平台上采用统一的行为。

#### Swing 和 AWT

AWT（Abstract Window Toolkit，抽象窗口工具）是一套早期的 Java GUI 开发工具，Swing 也是在 AWT 的基础上发展起来的。

AWT 的初衷是用来开发小型的图形界面程序，提供的功能较少，诸如剪切板、打印支持、键盘导航、弹出式菜单、滚动窗格等很多重要的功能在 AWT 中都不具备；此外，AWT 发生错误的几率也很高

Java 官方看到了 AWT 的不足，就开始着手开发新的 GUI 类库，以继续占领 Java GUI 开发的市场，这就是后来的 Swing。

Swing 弥补了 AWT 的不足，并对 AWT 进行了扩充，几乎支持了所有的常用控件和功能，它们不但更加漂亮，而且更加易用，真正实现了“一次编译，到处运行”的承诺。

目前，Swing 已经代替 AWT 成为 Java 图形界面设计的首选，相对于 AWT 来说，Swing 有过之而无不及。

### Swing 概念

Swing 是新一代的图形界面工具。使用 Swing 来开发图形界面比 AWT 更加优秀，因为 Swing 是一种轻量级组件，它采用纯 Java 实现，不再依赖于本地平台的图形界面，所以可以在所有平台上保持相同的运行效果，对跨平台支持比较出色。除此之外，Swing 提供了比 AWT 更多的图形界面组件，因此可以开发出美观的图形界面程序。

#### Swing 类库结构

![图1](https://ae01.alicdn.com/kf/U18270a17da5a43829ad68f2dbf8450c7r.png)


从图 1 可以看出，Swing 组件除了 AbstmctButton 类之外都以 J 开头。Swing 容器组件直接继承 AWT 类库中的容器组件类，其他大部分组件都是继承 JComponet 组件。组件可以划分为容器组件和非容器组件，容器组件包括 JFmme 和 JDialog。其中 JComponent 定义了非容器类的轻量级组件（JBntton、JPanel、JMenu 等）

#### Swing 包

Swing 类库由许多包组成，通过这些包中的类相互协作来完成 GUI 设计。其中，javax.swing 包是 Swing 提供的最大包，它包含将近 100 个类和 25 个接口。几乎所有 Swing 组件都在该包中。表 1 列出了常用的 Swing 包。



| 包名称 | 描述 |
| --- | --- |
| javax.swing | 提供一组“轻量级”组件，尽量让这些组件在所有平台上的工作方式都相同 |
| javax.swing.border | 提供围绕 Swing 组件绘制特殊边框的类和接口 |
| javax.swing.event | 提供 Swing 组件触发的事件 |
| javax.swing.filechooser | 提供 JFileChooser 组件使用的类和接口 |
| javax.swing.table | 提供用于处理 javax.swing.JTable 的类和接口 |
| javax.swing.text | 提供类 HTMLEditorKit 和创建 HTML 文本编辑器的支持类 | 
| javax.swing.tree | 提供处理 javax.swingJTree 的类和接口 |

javax.swing.event 包中定义了事件和事件监听器类，javax.swing.event 包与 AWT 的 event 包类似。Java.awt.event 和 javax.swing.event 都包含事件类和监听器接口，它们分别响应由 AWT 组件和 Swing 组件触发的事件。

例如，当在树组件中需要节点扩展（或折叠）的通知时，则要实现 Swing 的 TreeExpansionListener 接口，并把一个 TreeExpansionEvent 实例传送给 TreeExpansionListener 接口中定义的方法，而 TreeExpansionListener 和 TreeExpansionEvent 都是在 swing.event 包中定义的。

虽然 Swing 的表格组件（JTable）在 javax.swing 包中，但它的支持类却在 javax.swing.table 包中。表格模型、图形绘制类和编辑器等也都在 javax.swing.table 包中。

与 JTable 类一样，Swing 中的树 JTree（用于按层次组织数据的结构组件）也在 javax.swing 包中，而它的支持类却在 javax.swing.tree 包中。javax.swing.tree 包提供树模型、树节点、树单元编辑类和树绘制类等支持类。

#### Swing 容器

创建图形用户界面程序的第一步是创建一个容器类以容纳其他组件，常见的窗口就是一种容器。容器本身也是一种组件，它的作用就是用来组织、管理和显示其他组件。

##### Swing 中容器可以分为两类：顶层容器和中间容器。

顶层容器是进行图形编程的基础，一切图形化的东西都必须包括在顶层容器中。顶层容器是任何图形界面程序都要涉及的主窗口，是显示并承载组件的容器组件。在 Swing 中有三种可以使用的顶层容器，分别是 JFrame、JDialog 和 JApplet。
1. JFrame：用于框架窗口的类，此窗口带有边框、标题、关闭和最小化窗口的图标。带 GUI 的应用程序至少使用一个框架窗口。
2. JDialog：用于对话框的类。
3. JApplet：用于使用 Swing 组件的 Java Applet 类。

中间容器是容器组件的一种，也可以承载其他组件，但中间容器不能独立显示，必须依附于其他的顶层容器。常见的中间容器有 JPanel、JScrollPane、JTabbedPane 和 JToolBar。
* JPanel：表示一个普通面板，是最灵活、最常用的中间容器。
* JScrollPane：与 JPanel 类似，但它可在大的组件或可扩展组件周围提供滚动条。
* JTabbedPane：表示选项卡面板，可以包含多个组件，但一次只显示一个组件，用户可在组件之间方便地切换。
* JToolBar：表示工具栏，按行或列排列一组组件（通常是按钮）。

### Swing 优缺点

1. 与直觉不太一致：Swing的GUI上的各种组件如果添加的面板过多的话，就造成各个组件的层次很深，处理类似focus管理这样的问题就很麻烦，坐标的转换也很复杂，由于父子关系过多，您不看代码只看GUI，凭直觉难以区分组件的父子关系。


2. 布局上的困难：使用Swing开发界面的程序员会发现，即使Swing提供了这么多布局管理器，然而您想通过这些布局管理器做出很专业的界面却非常难，因为布局管理器非常依赖父容器和子组件的各种状态，尽管Swing最新的版本提供了类似组件和容器间隔的方法，然而还没有被大部分布局管理器采用，其实并不是布局管理器不够强大的问题，事实上，很多专业的界面需要从组件级别做出良好的定义，另外，不少Swing组件会根据容器的大小进行绘制，这也造成了很多不确定性，很多人喜欢使用NullLayout，可能就是这个原因，客户需要的是一个稳定的，可预知的界面，如果使用了布局管理器，会发现界面在不同的系统下展示的不同


3. 使用上的困扰：Swing组件本身由于不能分清是组件还是容器，很多容器方法比如setEnabled就没有效果，需要写代码遍历所有子组件，调用所 有的子组件相同的方法，而类似设置透明的方法也有这个问题，如果设置某个容器透明，也需要设置所有的子组件的透明属性，组件和容器的很多方法没有很好的定 义，这对了解Swing结构的人不是问题，但是对于熟悉别的ＧＵＩ类库的人就产生了很大的困惑，因为不少容器上的方法调用后是没有效果的。


总得来说，对Composite设计模式应该慎用，如果一定要用，一定要良好的定义组件（Component）和容器（Container）的边界，避免很多功能陷入没有意义的父子遍历例程，增加了复杂性。


### HelloWord

> 本示例来自菜鸟教程

```
import javax.swing.*;
public class HelloWorldSwing {
    /**{
     * 创建并显示GUI。出于线程安全的考虑，
     * 这个方法在事件调用线程中调用。
     */
    private static void createAndShowGUI() {
        // 确保一个漂亮的外观风格
        JFrame.setDefaultLookAndFeelDecorated(true);

        // 创建及设置窗口
        JFrame frame = new JFrame("HelloWorldSwing");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // 添加 "Hello World" 标签
        JLabel label = new JLabel("Hello World");
        frame.getContentPane().add(label);

        // 显示窗口
        frame.pack();
        frame.setVisible(true);
    }

    public static void main(String[] args) {
        // 显示应用 GUI
        javax.swing.SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                createAndShowGUI();
            }
        });
    }
}
```
