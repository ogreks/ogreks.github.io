---
title: 关于 Java Set、HashSet 的使用
date: 2019-12-22 10:00:00
categories:
 - java
tags:
 - java
 - java Set
 - java HashSet
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## Set 和 HashSet

Set 接口也是 Collection 接口的子接口, 它有一个很重要也是很常用的实现类----HashSet.  
Set 是元素无需并且不包含重复元素的Collection(List可以重复), 被称为集  

HashSet 由哈希表(实际上是一个HashMap实例)支持. 它不保证set的迭代顺序; 特别是它不保证该顺序恒久不变  

示例代码:
> 假设现在学生们要做项目，每个项目有一个组长，由组长来组织组员，我们便来实现项目组的管理吧。

``` 
// PD.java
import java.util.HashSet;
import java.util.Set;
/*
 * 项目组长类
 */
public class PD {

    public String id;
    public String name;
    //集合后面的<>代表泛型的意思
    //泛型是规定了集合元素的类型
    public Set<Student> students;
    public PD(String id, String name){
        this.id = id;
        this.name = name;
        this.students = new HashSet<Student>();
    }
}
```

``` 
/**
 * 学生类
 */
 // Student.java
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

管理成员类：

```
// SetTest.java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

public class SetTest {

    public List<Student> students;

    public SetTest() {
        students = new ArrayList<Student>();
    }

    /*
     * 用于往students中添加学生
     */
    public void testAdd() {
        //创建一个学生对象，并通过调用add方法，添加到学生管理List中
        Student st1 = new Student("1", "张三");
        students.add(st1);

        //添加到List中的类型均为Object，所以取出时还需要强转

        Student st2 = new Student("2","李四");
        students.add(st2);

        Student[] student = {new Student("3", "王五"),new Student("4", "马六")};
        students.addAll(Arrays.asList(student));

        Student[] student2 = {new Student("5", "周七"),new Student("6", "赵八")};
        students.addAll(Arrays.asList(student2));

    }

    /**
     * 通过for each 方法访问集合元素
     * @param args
     */
    public void testForEach() {
        System.out.println("有如下学生（通过for each）：");
        for(Object obj:students){
            Student st = (Student)obj;
            System.out.println("学生：" + st.id + ":" + st.name);
        }
    }

    public static void main(String[] args){
        SetTest st = new SetTest();
        st.testAdd();
        st.testForEach();
        PD pd = new PD("1","张老师");
        System.out.println("请：" + pd.name + "选择小组成员！");
        //创建一个 Scanner 对象，用来接收从键盘输入的学生 ID
        Scanner console = new Scanner(System.in);

        for(int i = 0;i < 3; i++){
            System.out.println("请输入学生 ID");
            String studentID = console.next();
            for(Student s:st.students){
                if(s.id.equals(studentID)){
                    pd.students.add(s);
                }
            }
        }
        st.testForEachForSer(pd);
        // 关闭 Scanner 对象
        console.close();
    }
    //打印输出，老师所选的学生！Set里遍历元素只能用foreach 和 iterator 
    //不能使用 get() 方法，因为它是无序的，不能想 List 一样查询具体索引的元素
    public void testForEachForSer(PD pd){
        for(Student s: pd.students) {
        System.out.println("选择了学生：" + s.id + ":" + s.name);
        }
    }

}
```

编译运行:

```
$ javac PD.java SetTest.java Student.java
$ java SetTest
有如下学生（通过for each）：
学生：1:张三
学生：2:李四
学生：3:王五
学生：4:马六
学生：5:周七
学生：6:赵八
请：张老师选择小组成员！
请输入学生 ID
4
请输入学生 ID
5
请输入学生 ID
6
选择了学生：4:马六
选择了学生：5:周七
选择了学生：6:赵八
```

