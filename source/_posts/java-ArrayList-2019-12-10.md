---
title: 关于 java ArrayList
date: 2019-12-10 02:45:55
categories:
 - java
 - java 基础
tags:
 - java
 - java ArrayList
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## ArrayList 类的使用示例

List是一个借口,***不能实例化***,需要一个具体类来实现实例化.  
List集合中的对象按照一定的顺序排放,里面的内容可以重复.  
List借口实现的类有：ArrayList(实现动态数组),Vector(实现动态数组),LinkedList(实现链表),Stack(实现堆栈).  

List在Collection基础上增加的方法：  

| 方法 | 返回值 | 说明 |
| ------ | --------- | --------- |
| add(int index, E element) | void | 在列表的指定位置插入指定元素(可选操作) |
| addAll(int index, Collection<? extends E> c) | boolean | 将指定 collection 中的所有元素都插入到列表中的指定位置（可选操作） |
| get(int index) | E | 返回列表中指定位置的元素 |
| indexOf(Object o) | int | 返回此列表中第一次出现的指定元素的索引；如果此列表不包含该元素，则返回 -1 |
| lastIndexOf(Object o) | int | 返回此列表中最后出现的指定元素的索引；如果列表不包含此元素，则返回 -1 |
| listIterator() | ListIterator<E> | 返回此列表元素的列表迭代器（按适当顺序) |
| listIterator(int index) | ListIterator<E> | 返回此列表元素的列表迭代器（按适当顺序）,从列表的指定位置开始 |
| remove(int index) | E | 移除列表中指定位置的元素（可选操作） |
| set(int index,E element) | E | 用指定元素替换列表中指定位置的元素（可选操作) |
| subList(int fromIndex, int toIndex) | List<E> | 返回列表中指定的fromIndex(包括)和toIndex(不包括)之间的部分视图 |


> ArrayList 类实现了一个可增长的动态数组, 位于java.util.ArrayList<E>. 
> 实现了List接口, 它可以存储不同的类型的对象（包括null在内）,
> 而数组则只能存放特点数据类型的值  

### ArrayList

学校的教务系统会对学生进行统一的管理，每一个学生都会有一个学号和学生姓名  
我们在维护整个系统的时候，大多数操作是对学生的添加、插入、删除、修改等操作  

```
/**
 * 学生类
 */
public class Student {
    public String id;
    public String name;
    public Student(String id, String name){
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                '}';
    }
}
```

对学生操作类，通过学生列表来管理学生  

```
import java.util.*;


public class ListTest {


    //集合后面的<>代表泛型的意思
    //泛型是规定了集合元素的类型
    /**
     * 用于存放学生的List
     */
    public List<Student> students;


    public ListTest() {
        this.students = new ArrayList<Student>();
    }

    /**
     * 用于往students中添加学生
     */
    public void testAdd() {
        // 创建一个学生对象，并通过调用add方法，添加到学生管理List中
        Student st1 = new Student("1", "张三");
        students.add(st1);

        // 取出 List中的Student对象 索引为0 也就是第一个
        Student temp = students.get(0);
        System.out.println("添加了学生：" + temp.id + ":" + temp.name);

        Student st2 = new Student("2", "李四");
        //添加到list中，插入到索引为0的位置，也就是第一个
        students.add(0, st2);
        Student temp2 = students.get(0);
        System.out.println("添加了学生：" + temp2.id + ":" + temp2.name);

        // 对象数组的形式添加
        Student[] student = {new Student("3", "王五"), new Student("4", "马六")};

        // Arrays类包含用来操作数组（比如排序和搜索）的各种方法，asList() 方法用来返回一个受指定数组支持的固定大小的列表
        students.addAll(Arrays.asList(student));
        Student temp3 = students.get(2);
        Student temp4 = students.get(3);
        System.out.println("添加了学生：" + temp3.id + ":" + temp3.name);
        System.out.println("添加了学生：" + temp4.id + ":" + temp4.name);
        Student[] student2 = {new Student("5", "周七"), new Student("6", "赵八")};
        students.addAll(2, Arrays.asList(student2));
        Student temp5 = students.get(2);
        Student temp6 = students.get(3);
        System.out.println("添加了学生：" + temp5.id + ":" + temp5.name);
        System.out.println("添加了学生：" + temp6.id + ":" + temp6.name);
    }


    /**
     * 取得List中的元素的方法
     */
    public void testGet() {
        int size = students.size();
        for (int i = 0; i < size; i++) {
            Student st = students.get(i);
            System.out.println("学生：" + st.id + ":" + st.name);

        }
    }


    /**
     * 通过迭代器来遍历
     * 迭代器的工作是遍历并选择序列中的对象，Java 中 Iterator 只能单向移动
     */
    public void testIterator() {
        // 通过集合的iterator方法，取得迭代器实例
        Iterator<Student> it = students.iterator();
        System.out.println("有如下学生（通过迭代器访问）：");
        while (it.hasNext()) {

            Student st = it.next();
            System.out.println("学生" + st.id + ":" + st.name);
        }
    }

    /**
     * 通过for each 方法访问集合元素
     *
     */
    public void testForEach() {
        System.out.println("有如下学生（通过for each）：");
        for (Student obj : students) {
            Student st = obj;
            System.out.println("学生：" + st.id + ":" + st.name);
        }
        //使用java8 Steam将学生排序后输出
        students.stream()//创建Stream
                //通过学生id排序
                .sorted(Comparator.comparing(x -> x.id))
                //输出
                .forEach(System.out::println);
    }

    /**
     * 修改List中的元素
     *
     */
    public void testModify() {
        students.set(4, new Student("3", "吴酒"));
    }

    /**
     * 删除List中的元素
     *
     */
    public void testRemove() {
        Student st = students.get(4);
        System.out.println("我是学生：" + st.id + ":" + st.name + "，我即将被删除");
        students.remove(st);
        System.out.println("成功删除学生！");
        testForEach();
    }


    public static void main(String[] args) {
        ListTest lt = new ListTest();
        lt.testAdd();
        lt.testGet();
        lt.testIterator();
        lt.testModify();
        lt.testForEach();
        lt.testRemove();

    }
}
```

编译运行结果  
```
$ javac Student.java ListTest.java
$ java ListTest

添加了学生：1:张三
添加了学生：2:李四
添加了学生：3:王五
添加了学生：4:马六
添加了学生：5:周七
添加了学生：6:赵八
学生：2:李四
学生：1:张三
学生：5:周七
学生：6:赵八
学生：3:王五
学生：4:马六
有如下学生（通过迭代器访问）：
学生2:李四
学生1:张三
学生5:周七
学生6:赵八
学生3:王五
学生4:马六
有如下学生（通过for each）：
学生：2:李四
学生：1:张三
学生：5:周七
学生：6:赵八
学生：3:吴酒
学生：4:马六
Student{id='1', name='张三'}
Student{id='2', name='李四'}
Student{id='3', name='吴酒'}
Student{id='4', name='马六'}
Student{id='5', name='周七'}
Student{id='6', name='赵八'}
我是学生：3:吴酒，我即将被删除
成功删除学生！
有如下学生（通过for each）：
学生：2:李四
学生：1:张三
学生：5:周七
学生：6:赵八
学生：4:马六
Student{id='1', name='张三'}
Student{id='2', name='李四'}
Student{id='4', name='马六'}
Student{id='5', name='周七'}
Student{id='6', name='赵八'}
```

在上面的代码中, 用到了Arrays类,Arrays 包含用来操作数组(比如排序和搜索)的各种方法  
asList()方法用来返回一个受指定数组支持的固定大小的列表  
