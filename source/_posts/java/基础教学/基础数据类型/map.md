---
title: 关于 Java Map 的使用
date: 2019-12-21 10:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java Map
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---


## java Map 

Map 接口也是一个非常重要的集合接口, 用于存储键/值对.  
Map 中的元素都是成对出现的,键值对就想数组的索引与数组的内容关系一样, 将一个键映射到一个值的对象.  
一个映射不能包含重复的键；每个键最多只能映射到一个值.  
我们可以通过键去找到相应的值.  

```
key =>(映射) value
```

value 可以存储任意类型的对象,我们可以根据key键快速查找value.   
Map 中的键/值对以Entry类型的对象实例形式存在.  

Map 方法:

| 方法 | 返回值 | 说明 |  
| ----- | ----- | ------ |
| clear() | void | 从此映射中一处所用映射关系(可选操作)  |
| containsKey(Object key) | boolean | 如果此映射包含指定键的映射关系,则返回 true |
| containsValue(Object value) | boolean | 如果此映射将一个或多个映射到指定值,则返回true |
| entrySet() | Set<Map.Entry<K,V>> | 返回此映射中包含的映射关系的Set视图 |
| equals(Object o) | boolean | 比较指定的对象与此映射是否相等 |
| get(Object key) | V | 返回指定键所映射的值;如果此映射不包含该键的映射关系,则返回null |
| hashCode() | int | 返回此映射的哈希码值 |
| isEmpty() | boolean | 如果此映射为包含键-值映射关系,则返回true |
| keySet() | Set<K> | 返回此映射中包含的键的Set视图 |
| put(K key,Vvalue) | V | 将指定的值与此映射中的指定键关联（可选操作） |
| putAll(Map<? extends K, ? extends V>m) | void | 从指定映射中将所有映射关系复制到此映射中（可选操作） |
| remove(Object key) | V | 如果存在一个键的映射关系,则将其从此映射中移除（可选操作） |
| size | int | 返回此映射中的键-值映射关系数 |
| values() | Collection<V> | 返回此映射中包含的值的Collection 视图 |

HashMap 是基于哈希表的Map接口的一个重要实现类. HashMap 中的Entry对象是无需排列的,key值和value值都可以为null,但是一个HashMap 只能有一个key值为null的映射(key值不可重复)  

示例代码:
```
// Course.java
public class Course {
    public String id;
    public String name;
    public Course(String id, String name){
        this.id = id;
        this.name = name;
    }
}
```

```
// MapTest.java
import java.util.HashMap;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Scanner;
import java.util.Set;

public class MapTest {

    /**
     * 用来承装课程类型对象
     */
    public Map<String, Course> courses;

    /**
     * 在构造器中初始化 courses 属性
     * @param args
     */
    public MapTest() {
        this.courses = new HashMap<String, Course>();
    }

    /**
     * 测试添加：输入课程 ID，判断是否被占用
     * 若未被占用，输入课程名称，创建新课程对象
     * 并且添加到 courses 中
     * @param args
     */
    public void testPut() {
        //创建一个 Scanner 对象，用来获取输入的课程 ID 和名称
        Scanner console = new Scanner(System.in);

        for(int i = 0; i < 3; i++) {
            System.out.println("请输入课程 ID：");
            String ID = console.next();
            //判断该 ID 是否被占用
            Course cr = courses.get(ID);
            if(cr == null){
                //提示输入课程名称
                System.out.println("请输入课程名称：");
                String name = console.next();
                //创建新的课程对象
                Course newCourse = new Course(ID,name);
                //通过调用 courses 的 put 方法，添加 ID-课程映射
                courses.put(ID, newCourse);
                System.out.println("成功添加课程：" + courses.get(ID).name);
            }
            else {
                System.out.println("该课程 ID 已被占用");
                continue;
            }
        }
    }

    /**
     * 测试 Map 的 keySet 方法
     * @param args
     */

    public void testKeySet() {
        //通过 keySet 方法，返回 Map 中的所有键的 Set 集合
        Set<String> keySet = courses.keySet();
        //遍历 keySet，取得每一个键，在调用 get 方法取得每个键对应的 value
        for(String crID: keySet) {
            Course cr = courses.get(crID);
            if(cr != null){
                System.out.println("课程：" + cr.name);
            }
        }
    }

    /**
     * 测试删除 Map 中的映射
     * @param args
     */
    public void testRemove() {
        //获取从键盘输入的待删除课程 ID 字符串
        Scanner console = new Scanner(System.in);
        while(true){
            //提示输出待删除的课程 ID
            System.out.println("请输入要删除的课程 ID！");
            String ID = console.next();
            //判断该 ID 是否对应的课程对象
            Course cr = courses.get(ID);
            if(cr == null) {
                //提示输入的 ID 并不存在
                System.out.println("该 ID 不存在！");
                continue;
            }
            courses.remove(ID);
            System.out.println("成功删除课程" + cr.name);
            break;
        }
    }

    /**
     * 通过 entrySet 方法来遍历 Map
     * @param args
     */
    public void testEntrySet() {
        //通过 entrySet 方法，返回 Map 中的所有键值对
        Set<Entry<String,Course>> entrySet = courses.entrySet();
        for(Entry<String,Course> entry: entrySet) {
            System.out.println("取得键：" + entry.getKey());
            System.out.println("对应的值为：" + entry.getValue().name);
        }
    }

    /**
     * 利用 put 方法修改Map 中的已有映射
     * @param args
     */
    public void testModify(){
        //提示输入要修改的课程 ID
        System.out.println("请输入要修改的课程 ID：");
        //创建一个 Scanner 对象，去获取从键盘上输入的课程 ID 字符串
        Scanner console = new Scanner(System.in);
        while(true) {
            //取得从键盘输入的课程 ID
            String crID = console.next();
            //从 courses 中查找该课程 ID 对应的对象
            Course course = courses.get(crID);
            if(course == null) {
                System.out.println("该 ID 不存在！请重新输入！");
                continue;
            }
            //提示当前对应的课程对象的名称
            System.out.println("当前该课程 ID，所对应的课程为：" + course.name);
            //提示输入新的课程名称，来修改已有的映射
            System.out.println("请输入新的课程名称：");
            String name = console.next();
            Course newCourse = new Course(crID,name);
            courses.put(crID, newCourse);
            System.out.println("修改成功！");
            break;
        }
    }

    public static void main(String[] args) {
        MapTest mt = new MapTest();
        mt.testPut();
        mt.testKeySet();
        mt.testRemove();
        mt.testModify();
        mt.testEntrySet();

    }
}
```

运行结果:
``` 
$ javac Course.java MapTest.java
$ java MapTest
请输入课程 ID：
1
请输入课程名称：
语文
成功添加课程：语文
请输入课程 ID：
1
该课程 ID 已被占用
请输入课程 ID：
2
请输入课程名称：
数学
成功添加课程：数学
课程：语文
课程：数学
请输入要删除的课程 ID！
1
成功删除课程语文
请输入要修改的课程 ID：
2
当前该课程 ID，所对应的课程为：数学
请输入新的课程名称：
英语
修改成功！
取得键：2
对应的值为：英语
```
