## Set集合

### Set集合遍历

没有索引、只能通过迭代器或增强for循环遍历

1.

```java
Set<String> set = new HashSet<>();
Iterator<String> it = set.iterator();
while (it.hasNext()) {
    String string = it.next();
    System.out.println(string);
}
```

2.

```java
Set<String> set = new HashSet<>();
for(String string:set) {
    System.out.println(string);
}
```



### Set集合排序

#### TreeSet集合概述和特点

TreeSet集合概述

- 元素有序，可以按照一定的规则进行排序，具体排序方式取决于构造方法
  - TreeSet()：根据其元素的**自然排序**进行排序
  - TreeSet(Comparator comparator) ：根据指定的**比较器**进行排序

#### 自然排序Comparable

* 实现步骤
  * 所谓自然排序，就是让元素所属的类实现Comparable接口，重写compareTo(T o)方法

* 代码实现
  * 学生类

```java
class Student implements Comparable<Student> {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public int compareTo(Student other) {
        int num = this.age - other.age;
        // 年龄相同时，按照姓名的字母顺序排序
        int num2 = num == 0 ? this.name.compareTo(other.name) : num;
        return num2;
    }
}
```
* 测试类


```java
public class TreeSetDemo02 {
    public static void main(String[] args) {
        //创建集合对象
        TreeSet<Student> ts = new TreeSet<Student>();

        //创建学生对象
        Student s1 = new Student("xishi", 29);
        Student s2 = new Student("wangzhaojun", 28);

        //把学生添加到集合
        ts.add(s1);
        ts.add(s2);

        //遍历集合
        for (Student s : ts) {
            System.out.println(s.getName() + "," + s.getAge());
        }
    }
}
```

#### 比较器排序Comparator的使用

* 实现步骤

  * 比较器排序，就是让集合构造方法接收Comparator的实现类对象，重写compare(T o1,T o2)方法

* 代码实现

  - 学生类
```java
public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
```

  - 测试类

    ```java
    public class TreeSetDemo {
        public static void main(String[] args) {
            //创建集合对象
            TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
                @Override
                public int compare(Student s1, Student s2) {
                    return s1.age-s2.age;//return o1-o2 <=>return o1.compareTo(o2) 返回负数,表明不需要交换顺序
                }
            });
    
            //创建学生对象
            Student s1 = new Student("xishi", 29);
            Student s2 = new Student("wangzhaojun", 28);
    
            //把学生添加到集合
            ts.add(s1);
            ts.add(s2);
    
            //遍历集合
            for (Student s : ts) {
                System.out.println(s.getName() + "," + s.getAge());
            }
        }
    }
    ```

## map集合

### Map集合的遍历

```java
import java.util.Map.Entry;
Map.Entry是Map的一个内部接口,它表示Map中的一个实体（一个key-value对）
```

- 获取所有键值对对象的集合
  - Set<Map.Entry<K,V>> entrySet()：获取所有键值对对象的集合
- 遍历键值对对象的集合，得到每一个键值对对象
  - 用增强for实现，得到每一个Map.Entry

### map集合排序

#### 1.key排序

~~~java

public class TreeMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new TreeMap<String, String>(new Comparator<String>() {
                    public int compare(String obj1, String obj2) {
                        // 降序排序
                        return obj2.compareTo(obj1);
                    }
                });
        // lambda表达式写法
	// Map<String,String> map1 = new TreeMap<>((o1,o2)->o2.compareTo(o1)); 
        map.put("b", "ccccc");
        map.put("d", "aaaaa");
        map.put("c", "bbbbb");
        map.put("a", "ddddd");
        
        Set<String> keySet = map.keySet();
        Iterator<String> iter = keySet.iterator();
        while (iter.hasNext()) {
            String key = iter.next();
            System.out.println(key + ":" + map.get(key));
        }
    }
}

~~~

#### 2.value排序

##### 1.借助List排序

对value排序我们就需要借助于`Collections的sort(List<T> list, Comparator<? super T> c)`方法，该方法根据指定比较器产生的顺序对指定列表进行排序。

```java
/*

*/

public class TreeMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new TreeMap<String, String>();
        map.put("a", "ddddd");
        map.put("c", "bbbbb");
        map.put("d", "aaaaa");
        map.put("b", "ccccc");
        
        //这里将map.entrySet()转换成list  
        
        
        List<Map.Entry<String,String>> lists = new ArrayList<>((Collection<? extends Entry<String, String>>) map.entrySet());
        //然后通过比较器来实现排序
        Collections.sort(list,new Comparator<Map.Entry<String,String>>() {
            //升序排序
            public int compare(Entry<String, String> o1,Entry<String, String> o2) {
                return o1.getValue().compareTo(o2.getValue());
            }
            
        });
        
        //<=>Collections.sort(list,(o1,o2) -> o1.getValue().compareTo(o2.getValue());
        //list.sort(Comparator.comparing(Map.Entry::getValue));
        Map<String,String> map2 = new LinkedHashMap<>();
        for(Map.Entry<String,String> entry: list){
            map2.put(entry.getKey(), entry.getValue());
        }
        map2.forEach((o1, o2)-> System.out.println(o1+":"+o2)); 
    }
}
```

##### 2.Stream排序

```java
Map<Integer, String> map = new HashMap<>();
map.put(1, "Tommy");
map.put(3, "Jerry");
map.put(2, "Catty");
// 正序
map.values().stream().sorted().collect(Collectors.toList()).forEach(System.out::println);
// 逆序
map.values().stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList())
        .forEach(System.out::println);
```



## pair

```java
import javafx.util.Pair    
Pair<Integer, String> pair = new Pair<>(1, "One");
Integer key = pair.getKey();
String value = pair.getValue();
```

## Queue

### 1、比较器`Comparator`

```java
    private PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>(new MinHeapComparator());
    public static class MinHeapComparator implements Comparator<Integer> {
		@Override
		public int compare(Integer o1, Integer o2) {
			if (o2 < o1) {
				return 1;
			} else {
				return -1;
			}
		}
	}
```

### 2、`lambda`表达式

```java
PriorityQueue<Integer>minHeap = new PriorityQueue<Integer>((o1,o2) -> o1-o2);
PriorityQueue<Integer>minHeap = new PriorityQueue<Integer>((o1,o2) -> {return o1-o2;});
PriorityQueue<Integer>minHeap = new PriorityQueue<Integer>((o1,o2) ->Integer.compare(o1,o2));//比较器，所以需要两个变量
PriorityQueue<Integer>minHeap = new PriorityQueue<Integer>((o1,o2) -> o1.compareTo(o2));//类方法，只需要另一个比较变量即可
```

### 3、自然排序`Comparable`

```java
class Student implements Comparable<Student> {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public int compareTo(Student other) {
        int num = this.age - other.age;
        // 年龄相同时，按照姓名的字母顺序排序
        int num2 = num == 0 ? this.name.compareTo(other.name) : num;
        return num2;
    }
}

PriorityQueue<Student>minHeap = new PriorityQueue<Student>();
```

## array与list

### array与list转换

#### 1.int数组与List互转

```java
import org.apache.commons.lang3.ArrayUtils;
    @Test
    public void test5(){
        int[] array = {1, 2, 5, 5, 5, 5, 6, 6, 7, 2, 9, 2};
 
        /*int[]转list*/
        //方法一：需要导入apache commons-lang3  jar 
        List<Integer> list = new ArrayList<>(Arrays.asList(ArrayUtils.toObject(array)));
        //方法二：java8及以上版本
        List<Integer> list1 = Arrays.stream(array).boxed().collect(Collectors.toList());
 
        /*list转int[]*/
        //方法一：
        Integer[] intArr =  list.toArray(new Integer[list.size()]);
        //方法二：java8及以上版本
        int[] intArr1 =  list.stream().mapToInt(Integer::valueOf).toArray();
        
```

int数组转List还需要导入apache包，刷题中直接用遍历的方式转换吧。

#### 2.String数组与List互转

```java
List<String> list = new ArrayList<>(Arrays.asList(array));
String[] intArr =  list.toArray(new String[list.size()]);
```

### array与list排序

见Queue部分知识，排序有三种方法：1）比较器Comparator；2）lambda表达式；3）自然排序Comparable。

#### array排序(Arrays)

```java
String[] array = new String[0];
Arrays.sort(array, (o1, o2) -> o1.compareTo(o2));
```

#### list排序(Collections)

```java
List<String>list = new linkedList<>();
Collections.sort(list, (o1, o2) -> o1.compareTo(o2))
```

