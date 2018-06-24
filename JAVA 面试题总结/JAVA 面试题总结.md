# JAVA 面试题总结

## 一、JAVA SE

### 1. 面向对象的特征（简单谈谈面向对象，OOP编程）(重点)

#### - 抽象

==抽象是将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽象两方面==，抽象关注的永远是对象的行为和属性，并不关注这些行为的细节

#### - 继承 

==继承是从已有类得到继承信息创建新类的过程==。提供继承信息的类被称为父类（超类、基类）；得到继承信息的类被称为子类（派生类）。继承让变化中的软件系统有了一定的延续性。

#### - 封装

==通常认为封装是把数据和操作数据的方法绑定起来，对数据的访问只能通过已定义的接口==；我们在类中编写的方法就是对实现细节的一种封装；我们编写一个类就是对数据和数据操作的封装。

#### - 多态

简单的说就是用同样的对象引用调用同样的方法但是做了不同的事情（父类的引用指向子类的实例）。多态性分为编译时的多态性和运行时的多态性

- ==方法重载（overload）实现的是编译时的多态性（也称为前绑定）==：类中可以创建多个方法，它们具有相同的名字，但具有不同的参数和不同的定义。调用方法时通过传递给它们的不同参数个数和参数类型来决定具体使用哪个方法, ==无法以返回型别作为重载函数的区分标准==

- ==方法覆写（override）实现的是运行时的多态性（也称为后绑定）==:

  - 参数列表必须完全与被覆写的方法相同;
  - 返回的类型必须一直与被覆写的方法的返回类型相同
  - 访问修饰符的限制一定要大于被覆写方法的访问修饰符（public>protected>default>private）
  - 覆写方法一定不能抛出新的检查异常或者比被覆写方法申明更加宽泛的检查型异常

- ==运行时的多态是面向对象最精髓的东西==，要实现多态需要做两件事：

  1). 方法覆写（子类继承父类并覆写父类中已有的或抽象的方法）；

  2). 对象造型（用父类型引用引用子类型对象，这样同样的引用调用同样的方法就会根据子类对象的不同而表现出不同的行为）

### 2. 访问修饰符public,private,protected,以及不写（默认）时的区别？

| 修饰符    | 当前类 | 同 包 | 子 类 | 其他包 |
| --------- | ------ | ----- | ----- | ------ |
| public    | √      | √     | √     | √      |
| protected | √      | √     | √     | ×      |
| default   | √      | √     | ×     | ×      |
| private   | √      | ×     | ×     | ×      |

类的成员不写访问修饰时默认为default。默认对于同一个包中的其他类相当于公开（public），对于不是同一个包中的其他类相当于私有（private）。受保护（protected）对子类相当于公开，对不是同一包中的没有父子关系的类相当于私有。Java中，外部类的修饰符只能是public或默认，类的成员（包括内部类）的修饰符可以是以上四种。

### 3. String 是最基本的数据类型吗

答：不是。Java中的基本数据类型只有8个：byte、short、int、long、float、double、char、boolean；除了基本类型（primitive type），剩下的都是引用类型（reference type），Java 5以后引入的枚举类型也算是一种比较特殊的引用类型。

### 4. float f=3.4;是否正确？

答:不正确。==3.4是双精度数，将双精度型（double）赋值给浮点型（float）属于下转型（down-casting，也称为窄化）会造成精度损失==，因此需要强制类型转换float f =(float)3.4; 或者写成float f =3.4F。

### 5. short s1 = 1; s1 = s1 + 1;有错吗?short s1 = 1; s1 += 1;有错吗？

答：对于short s1 = 1; s1 = s1 + 1;由于1是int类型，因此s1+1运算结果也是int 型，需要强制转换类型才能赋值给short型。而short s1 = 1; s1 += 1;可以正确编译，==因为s1+= 1;相当于s1 = (short)(s1 + 1);其中有隐含的强制类型转换==。

### 6. int和Integer的编程题

```java
class AutoUnboxingTest {

    public static void main(String[] args) {
        Integer a = new Integer(3);
        Integer b = 3;                  // 将3自动装箱成Integer类型
        int c = 3;
        System.out.println(a == b);     // false 两个引用没有引用同一对象
        System.out.println(a == c);     // true a自动拆箱成int类型再和c比较
    }
}


public class Test03 {

    public static void main(String[] args) {
        Integer f1 = 100, f2 = 100, f3 = 150, f4 = 150;

        System.out.println(f1 == f2);
        System.out.println(f3 == f4);
    }
}
	简单的说，如果整型字面量的值在-128到127之间，那么不会new新的Integer对象，而是直接引用常量池中的Integer对象，所以上面的面试题中f1==f2的结果是true，而f3==f4的结果是false
```

### 7. &和&&的区别

&&之所以称为短路运算是因为，如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算,a && b 这个运算如果要为true，必须a和b均为true；||则相反，a || b 这个运算要为false，必须a和b都要为false

### 8. 解释内存中的栈(stack)、堆(heap)和方法区(method area)的用法

答：==通常我们定义一个基本数据类型的变量，一个对象的引用，还有就是函数调用的现场保存都使用JVM中的栈空间==

==而通过new关键字和构造器创建的对象则放在堆空间==，==方法区和堆都是各个线程共享的内存区域，用于存储已经被JVM加载的类信息、常量、静态变量==、JIT编译器编译后的代码等数据，==程序中的字面量（literal）如直接书写的100、"hello"和常量都是放在常量池中，常量池是方法区的一部分==

### 9. Math.round(11.5) 等于多少？Math.round(-11.5)等于多少？

答：Math.round(11.5)的返回值是12，Math.round(-11.5)的返回值是-11。==四舍五入的原理是在参数上加0.5然后进行下取整==

### 10. switch 是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？

答：在Java 5以前，switch(expr)中，expr只能是byte、short、char、int。从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，==从Java 7开始，expr还可以是字符串（String）==，但是长整型（long）在目前所有的版本中都是不可以的。

### 11. 数组有没有length()方法？String有没有length()方法？

答：数组没有length()方法，有length 的属性。String 有length()方法。JavaScript中，获得字符串的长度是通过length属性得到的，这一点容易和Java混淆。

### 12. 构造器（constructor）是否可被重写（override）？

答：构造器不能被继承，因此不能被重写，但可以被重载。

### 13. 两个对象值相同(x.equals(y) == true)，但却可有不同的hash code，这句话对不对？（重点）

答：不对，如果两个对象x和y满足x.equals(y) == true，它们的哈希码（hash code）应当相同。Java对于eqauls方法和hashCode方法是这样规定的：==(1)如果两个对象相同（equals方法返回true），那么它们的hashCode值一定要相同；(2)如果两个对象的hashCode相同，它们的equals方法并不一定相同==

==equals方法的四大特性：==

首先equals方法必须满足自反性（x.equals(x)必须返回true）、对称性（x.equals(y)返回true时，y.equals(x)也必须返回true）、传递性（x.equals(y)和y.equals(z)都返回true时，x.equals(z)也必须返回true）和一致性（当x和y引用的对象信息没有被修改时，多次调用x.equals(y)应该得到同样的返回值），而且对于任何非null值的引用x，x.equals(null)必须返回false

### 14. 是否可以继承String类？

答：String 类是final类，不可以被继承。

### 15. String和StringBuilder、StringBuffer的区别(重点)

答：Java平台提供了两种类型的字符串：String和StringBuffer/StringBuilder，它们可以储存和操作字符串。其中String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。

而StringBuffer/StringBuilder类表示的字符串对象可以直接进行修改。StringBuilder是Java 5中引入的，它和StringBuffer的方法完全相同，区别在于它是在单线程环境下使用的，因为它的所有方面都没有被synchronized修饰，因此它的效率也比StringBuffer要高。

```java
class StringEqualTest {

    public static void main(String[] args) {
        String s1 = "Programming";
        String s2 = new String("Programming");
        String s3 = "Program";
        String s4 = "ming";
        String s5 = "Program" + "ming";
        String s6 = s3 + s4;
        System.out.println(s1 == s2);		false
        System.out.println(s1 == s5);		true
        System.out.println(s1 == s6);		false
        System.out.println(s1 == s6.intern());		true
        System.out.println(s2 == s2.intern());		false
    }
}
```

1. String对象的intern方法会得到字符串对象在常量池中对应的版本的引用（如果常量池中有一个字符串与String对象的equals结果是true），如果常量池中没有对应的字符串，则该字符串将被添加到常量池中，然后返回常量池中字符串的引用；
2. 字符串的+操作其本质是创建了StringBuilder对象进行append操作，然后将拼接后的StringBuilder对象用toString方法处理成String对象，这一点可以用javap -c StringEqualTest.class命令获得class文件对应的JVM字节码指令就可以看出来

### 16. 为什么函数重载不可以根据返回类型区分？

因为调用时不能指定类型信息，编译器不知道你要调用哪个函数。
例如

```java
float max(int a, int b);
int max(int a, int b);
```

当调用max(1, 2);时无法确定调用的是哪个，单从这一点上来说，仅返回值类型不同的重载是不应该允许的。

==函数的返回值只是作为函数运行之后的一个“状态”，他是保持方法的调用者与被调用者进行通信的关键，但并不能作为某个方法的“标识”==

### 17. 描述一下JVM加载class文件的原理机制？

答：JVM中类的装载是由类加载器（ClassLoader）和它的子类来实现的，Java中的类加载器是一个重要的Java运行时系统组件，它负责在运行时查找和装入类文件中的类。

==由于Java的跨平台性，经过编译的Java源程序并不是一个可执行程序，而是一个或多个类文件==

1. 读取.class文件阶段：==类的加载是指把类的.class文件中的数据读入到内存中==，通常是创建一个字节数组读入.class文件，然后产生与所加载类对应的Class对象。加载完成后，Class对象还不完整，所以此时的类还不可用。
2. 连接阶段：当类被加载后就进入连接阶段，这一阶段包括验证、准备（==为静态变量分配内存并设置默认的初始值==）和解析（将符号引用替换为直接引用）三个步骤
3. 初始化阶段：==最后JVM对类进行初始化，包括：1)如果类存在直接的父类并且这个类还没有被初始化，那么就先初始化父类；2)如果类中存在初始化语句，就依次执行这些初始化语句==

### 18. char 型变量中能不能存贮一个中文汉字，为什么？

char型变量是用来存储Unicode编码的字符的，unicode编码字符集中包含了汉字

unicode编码占用两个字节，所以，char类型的变量也是占用两个字节

char表示的范围是0--65535

### 19. 静态嵌套类(Static Nested Class)和内部类（Inner Class）的不同？

答：Static Nested Class是被声明为静态（static）的内部类，它可以不依赖于外部类实例被实例化。而通常的内部类需要在外部类实例化后才能实例化

```java
class Outer {

    class Inner {}

    public static void foo() { new Inner(); }

    public void bar() { new Inner(); }

    public static void main(String[] args) {
        new Inner();
    }
}
```

注意：Java中非静态内部类对象的创建要依赖其外部类对象，上面的面试题中foo和main方法都是静态方法，静态方法中没有this，也就是说没有所谓的外部类对象，因此无法创建内部类对象，如果要在静态方法中创建内部类对象，可以这样做：

```java
new Outer().new Inner();
```

### 20. 抽象的（abstract）方法是否可同时是静态的（static）,是否可同时是本地方法（native），是否可同时被synchronized修饰？

答：都不能。抽象方法需要子类重写，而静态的方法是无法被重写的，因此二者是矛盾的。本地方法是由本地代码（如C代码）实现的方法，而抽象方法是没有实现的，也是矛盾的。synchronized和方法的实现细节有关，抽象方法不涉及实现细节，因此也是相互矛盾的。

### 21. 是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？

答：不可以，静态方法只能访问静态成员，因为非静态方法的调用要先创建对象，在调用静态方法时可能对象并没有被初始化。

### 22. 如何实现对象克隆？

答：有两种方式： 
  1). 实现Cloneable接口并重写Object类中的clone()方法,引用属性不会克隆，而是指向同一个引用对象； 
  2). 实现Serializable接口，通过对象的序列化和反序列化实现克隆（Serializable），可以实现真正的深度克隆

### 24. String s = new String("xyz");创建了几个字符串对象？

答：两个对象，一个是静态区的"xyz"，一个是用new创建在堆上的对象

### 25. 接口是否可继承（extends）接口？抽象类是否可实现（implements）接口？抽象类是否可继承具体类（concrete class）？

答：接口可以继承接口，而且支持多重继承。抽象类可以实现(implements)接口，抽象类可继承具体类也可以继承抽象类。

### 26. 一个".java"源文件中是否可以包含多个类（不是内部类）？有什么限制？

答：可以，但一个源文件中最多只能有一个公开类（public class）而且文件名必须和公开类的类名完全保持一致

### 27. 内部类可以引用它的包含类（外部类）的成员吗？有没有什么限制？

答：一个内部类对象可以访问创建它的外部类对象的成员，包括私有成员

### 28. Java 中的final关键字有哪些用法？

答：(1)修饰类：表示该类不能被继承；(2)修饰方法：表示方法不能被重写；(3)修饰变量：表示变量只能一次赋值以后值不能被修改（常量）

### 29. 类的加载顺序

```java
class A {

    static {
        System.out.print("1");
    }

    public A() {
        System.out.print("2");
    }
}

class B extends A{

    static {
        System.out.print("a");
    }

    public B() {
        System.out.print("b");
    }
}

public class Hello {

    public static void main(String[] args) {
        A ab = new B();
        ab = new B();
    }

}
```

答：执行结果：1a2b2b。==创建对象时构造器的调用顺序是：先初始化静态成员，然后调用父类构造器，再初始化非静态成员，最后调用自身构造器==

### 30. **数据类型之间的转换：** 

**- 如何将字符串转换为基本数据类型？** 
**- 如何将基本数据类型转换为字符串？**

答： 

- 调用基本数据类型对应的包装类中的方法parseXXX(String)或valueOf(String)即可返回相应基本类型； 


- 一种方法是将基本数据类型与空字符串（""）连接（+）即可获得其所对应的字符串；另一种方法是调用String 类中的valueOf()方法返回相应字符串

### 31. 打印昨天的当前时刻

```java
import java.time.LocalDateTime;
//java 8
class YesterdayCurrent {

    public static void main(String[] args) {
        LocalDateTime today = LocalDateTime.now();
        LocalDateTime yesterday = today.minusDays(1);

        System.out.println(yesterday);
    }
}
```

### 32. try{}里有一个return语句，那么紧跟在这个try后的finally{}里的代码会不会被执行，什么时候被执行，在return前还是后?

答：==会执行，在方法返回调用者前执行==

**注意：**在finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰

### 33. Java语言如何进行异常处理，关键字：throws、throw、try、catch、finally分别如何使用？

==Java的异常处理是通过5个关键词来实现的：try、catch、throw、throws和finally。==

一般情况下是用try来执行一段程序，如果系统会抛出（throw）一个异常对象，可以通过它的类型来捕获（catch）它，或通过总是执行代码块（finally）来处理；

try用来指定一块预防所有异常的程序；catch子句紧跟在try块后面，用来指定你想要捕获的异常的类型；throw语句用来明确地抛出一个异常；throws用来声明一个方法可能抛出的各种异常（当然声明异常时允许无病呻吟）；finally为确保一段代码不管发生什么异常状况都要被执行

### 34. 阐述final、finally、finalize的区别

- final：修饰符（关键字）有三种用法：如果一个类被声明为final，意味着它不能再派生出新的子类，即不能被继承，因此它和abstract是反义词。将变量声明为final，可以保证它们在使用中不被改变，被声明为final的变量必须在声明时给定初值，而在以后的引用中只能读取不可修改。被声明为final的方法也同样只能使用，不能在子类中被重写。 
- finally：通常放在try…catch…的后面构造总是执行代码块，这就意味着程序无论正常执行还是发生异常，这里的代码只要JVM不关闭都能执行，可以将释放外部资源的代码写在finally块中。 
- finalize：Object类中定义的方法，Java中允许使用finalize()方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。这个方法是由垃圾收集器在销毁对象时调用的，通过重写finalize()方法可以整理系统资源或者执行其他清理工作。


### 35. 阐述ArrayList、Vector、LinkedList的存储性能和特性（重点）

答：ArrayList 和Vector都是使用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢，Vector中的方法由于添加了synchronized修饰，因此Vector是线程安全的容器，但性能上较ArrayList差，因此已经是Java中的遗留容器。

LinkedList使用双向链表实现存储（将内存中零散的内存单元通过附加的引用关联起来，形成一个可以按序号索引的线性结构，这种链式存储方式与数组的连续存储方式相比，内存的利用率更高），按序号索引数据需要进行前向或后向遍历，但是插入数据时只需要记录本项的前后项即可，所以插入速度较快

### 36. Collection和Collections的区别

答：Collection是一个接口，它是Set、List等容器的父接口；Collections是个一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等等

### 37. List、Map、Set三个接口存取元素时，各有什么特点（重点）

### 38. TreeMap和TreeSet在排序时如何比较元素？Collections工具类中的sort()方法如何比较元素？（重点）

答：TreeSet要求存放的对象所属的类必须实现Comparable接口，该接口提供了比较元素的compareTo()方法，当插入元素时会回调该方法比较元素的大小。

TreeMap要求存放的键值对映射的==键必须实现Comparable接口从而根据键对元素进行排序==。

Collections工具类的sort方法有两种重载的形式，第一种要求传入的待排序容器中存放的对象比较实现Comparable接口以实现元素的比较；第二种不强制性的要求容器中的元素必须可比较，但是要求传入第二个参数，参数是Comparator接口的子类型（需要重写compare方法实现元素的比较），相当于一个临时定义的排序规则

### 39. Thread类的sleep()方法和对象的wait()方法都可以让线程暂停执行，它们有什么区别?

答：sleep()方法（休眠）是线程类（Thread）的静态方法，调用此方法会让当前线程暂停执行指定的时间，将执行机会（CPU）让给其他线程，但是对象的锁依然保持，因此休眠时间结束后会自动恢复（线程回到就绪状态，请参考第66题中的线程状态转换图）。

wait()是Object类的方法，调用对象的wait()方法导致当前线程放弃对象的锁（线程暂停执行），进入对象的等待池（wait pool），只有调用对象的notify()方法（或notifyAll()方法）时才能唤醒等待池中的线程进入等锁池（lock pool），如果线程重新获得对象的锁就可以进入就绪状态

> **补充：**可能不少人对什么是进程，什么是线程还比较模糊，对于为什么需要多线程编程也不是特别理解。简单的说：进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动，是操作系统进行资源分配和调度的一个独立单位；线程是进程的一个实体，是CPU调度和分派的基本单位，是比进程更小的能独立运行的基本单位。线程的划分尺度小于进程，这使得多线程程序的并发性高；进程在执行时通常拥有独立的内存单元，而线程之间可以共享内存。使用多线程的编程通常能够带来更好的性能和用户体验，但是多线程的程序对于其他程序是不友好的，因为它可能占用了更多的CPU资源。当然，也不是线程越多，程序的性能就越好，因为线程之间的调度和切换也会浪费CPU时间

### 40. 线程的sleep()方法和yield()方法有什么区别？

答： 
① ==sleep()方法给其他线程运行机会时不考虑线程的优先级，因此会给低优先级的线程以运行的机会；yield()方法只会给相同优先级或更高优先级的线程以运行的机会==； 
② ==线程执行sleep()方法后转入阻塞（blocked）状态，而执行yield()方法后转入就绪（ready）状态；== 
③ sleep()方法声明抛出InterruptedException，而yield()方法没有声明任何异常； 
④ sleep()方法比yield()方法（跟操作系统CPU调度相关）具有更好的可移植性

### 41. 当一个线程进入一个对象的synchronized方法A之后，其它线程是否可进入此对象的synchronized方法B？

答：==不能==。其它线程只能访问该对象的非同步方法，同步方法则不能进入。==因为非静态方法上的synchronized修饰符要求执行方法时要获得**对象的锁**，如果已经进入A方法说明对象锁已经被取走，那么试图进入B方法的线程就只能在等锁池（**注意不是等待池哦**）中等待对象的锁==

### 42. 请说出与线程同步以及线程调度相关的方法

答： 

- wait()：==使一个线程处于等待（阻塞）状态，并且释放所持有的对象的锁==； 

- sleep()：使一个正在运行的线程处于睡眠状态，是一个静态方法，调用此方法要处理InterruptedException异常； 

- notify()：==唤醒一个处于等待状态的线程，当然在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且与优先级无关；== 

- notityAll()：==唤醒所有处于等待状态的线程，该方法并不是将对象的锁给所有线程，而是让它们竞争，只有获得锁的线程才能进入就绪状态；==

  > 补充：Java 5通过Lock接口提供了显式的锁机制（explicit lock），增强了灵活性以及对线程的协调。Lock接口中定义了加锁（lock()）和解锁（unlock()）的方法，同时还提供了newCondition()方法来产生用于线程之间通信的Condition对象；此外，Java 5还提供了信号量机制（semaphore），信号量可以用来限制对某个共享资源进行访问的线程的数量。在对资源进行访问之前，线程必须得到信号量的许可（调用Semaphore对象的acquire()方法）；在完成对资源的访问后，线程必须向信号量归还许可（调用Semaphore对象的release()方法）

### 43. 编写多线程程序有几种实现方式？

答：Java 5以前实现多线程有两种实现方法：一种是继承Thread类；另一种是实现Runnable接口。两种方式都要通过重写run()方法来定义线程的行为，推荐使用后者，因为Java中的继承是单继承，一个类有一个父类，如果继承了Thread类就无法再继承其他类了，显然使用Runnable接口更为灵活

Java 5以后创建线程还有第三种方式：实现Callable接口，该接口中的call方法可以在线程执行结束时产生一个返回值；相较于实现Runnable 接口的方式，call方法可以具有返回值，并且还要抛出异常

还有第四种方式：线程池

### 44. 举例说明同步和异步

答：如果系统中存在临界资源（资源数量少于竞争资源的线程数量的资源），==例如正在写的数据以后可能被另一个线程读到，或者正在读的数据可能已经被另一个线程写过了，那么这些数据就必须进行同步存取（数据库操作中的排他锁就是最好的例子）==。==当应用程序在对象上调用了一个需要花费很长时间来执行的方法，并且不希望让程序等待方法的返回时，就应该使用异步编程，在很多情况下采用异步途径往往更有效率==。==事实上，所谓的同步就是指阻塞式操作，而异步就是非阻塞式操作==

### 45. 什么是线程池（thread pool）？

答：在面向对象编程中，创建和销毁对象是很费时间的，因为创建一个对象要获取内存资源或者其它更多资源。在Java中更是如此，虚拟机将试图跟踪每一个对象，以便能够在对象销毁后进行垃圾回收。所以提高服务程序效率的一个手段就是尽可能减少创建和销毁对象的次数，特别是一些很耗资源的对象创建和销毁，这就是”池化资源”技术产生的原因

线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销

在工具类Executors面提供了一些静态工厂方法，生成一些常用的线程池，如下所示： 

- newSingleThreadExecutor：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行

- newFixedThreadPool：创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程

- newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小

- newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求

  > 如果希望在服务器上使用线程池，强烈建议使用newFixedThreadPool方法来创建线程池，这样能获得更好的性能。


### 46. 线程的基本状态以及状态之间的关系？（重点）

![1527557109181](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527557109181.png)

> **说明：**其中Running表示==运行状态==，Runnable表示==就绪状态（万事俱备，只欠CPU）==，Blocked表示==阻塞状态==，阻塞状态又有多种情况，可能是因为调用wait()方法进入==等待池==，也可能是执行同步方法或同步代码块进入==等锁池==，或者是调用了sleep()方法或join()方法等待休眠或其他线程结束，或是因为发生了I/O中断。



### 47. 简述synchronized 和java.util.concurrent.locks.Lock的异同？（重点）

答：Lock是Java 5以后引入的新的API，和关键字synchronized相比主要相同点：Lock 能完成synchronized所实现的所有功能；主要不同点：==Lock有比synchronized更精确的线程语义和更好的性能，而且不强制性的要求一定要获得锁==。==synchronized会自动释放锁，而Lock一定要求程序员手工释放，并且最好在finally 块中释放（这是释放外部资源的最好的地方）==

### 48. Java中如何实现序列化，有什么意义？

答：序列化就是一种用来处理对象流的机制，所谓对象流也就是将对象的内容进行流化。可以对流化后的对象进行读写操作，也可将流化后的对象传输于网络之间。序列化是为了解决对象流读写操作时可能引发的问题（如果不进行序列化可能会存在数据乱序的问题）

要实现序列化，需要让一个类实现Serializable接口，该接口是一个标识性接口，标注该类对象是可被序列化的，然后使用一个输出流来构造一个对象输出流并通过writeObject(Object)方法就可以将实现对象写出（即保存其状态）；如果需要反序列化则可以用一个输入流建立对象输入流，然后通过readObject方法从流中读取对象。==序列化除了能够实现对象的持久化之外，还能够用于对象的深度克隆==

### 49. 如何用Java代码列出一个目录下所有的文件？

如果只要求列出当前文件夹下的文件名，代码如下所示：

```java
import java.io.File;

class Test12 {

    public static void main(String[] args) {
        File f = new File("/Users/Hao/Downloads");
        for(File temp : f.listFiles()) {
            if(temp.isFile()) {
                System.out.println(temp.getName());
            }
        }
    }
}
```

如果需要对文件夹继续展开，代码如下所示：

```java
	@Test
	void test() {
		File file = new File("D:\\HelloWorld");
		findFiles(file, 0);
	}
	public void findFiles(File file, int i){
		for(int k=0;k<i;k++) {
			System.out.print("|  ");
		}
		System.out.print("|--");
		System.out.println(file.getName());
		
		if(file.isDirectory()){
			File[] listFiles = file.listFiles();
			if(listFiles.length>0) {
				for(File f : listFiles) {
					findFiles(f, i+1);			//递归，值传递
				}
			}
		}
	}
```

- 递归删除文件下的文件

  ```java
  	/**
  	 * 循环递归删除文件
  	 * @param file
  	 */
  	public static void deleteFile(File file) {
  		if(file.isDirectory()) {
  			File[] files = file.listFiles();
  			for(File  f: files) {
  				deleteFile(f);
  			}
  		}
  		file.delete();
  	}
  ```

- 递归删除文件夹下的文件完善版

  ```java
  /**
  	 * 删除指定文件夹下的所有文件
  	 * 先判断，后删除，逻辑缜密，节省资源
  	 * @param file
  	 */
  public class Exercise4 {
  	public static void main(String[] args) {
  		File file = new File("C:\\var");
  		delete(file);
  	}

  	public static void delete(File file) {
  		if (file.isFile()) {
  			System.out.println("传入的是文件，请传入文件夹");
  			return;
  		}
  		File[] listFiles = file.listFiles();
  		if (listFiles.length == 0) {
  			System.out.println("传入的是空文件夹，请检查参数");
  			return;
  		}

  		for (File f : listFiles) {
  			deleteFile(f);
  		}
  	}
  ```

### 50. Statement和PreparedStatement有什么区别？哪个性能更好？

答：与Statement相比，

①PreparedStatement接口代表预编译的语句，它主要的优势在于可以减少SQL的编译错误并增加SQL的安全性（减少SQL注射攻击的可能性）；

②PreparedStatement中的SQL语句是可以带参数的，避免了用字符串连接拼接SQL语句的麻烦和不安全；

③当批量处理SQL或频繁执行相同的查询时，PreparedStatement有明显的性能上的优势，由于数据库可以将编译优化后的SQL语句缓存起来，下次执行相同结构的语句时就会很快（不用再次编译和生成执行计划）

### 51. 什么是DAO模式？

答：DAO（Data Access Object）顾名思义是一个为数据库或其他持久化机制提供了抽象接口的对象，在不暴露底层持久化方案实现细节的前提下提供了各种数据访问操作；

用程序设计语言来说，就是建立一个接口，接口中定义了此应用程序中将会用到的所有事务方法。在这个应用程序中，当需要和数据源进行交互的时候则使用这个接口，并且编写一个单独的类来实现这个接口，在逻辑上该类对应一个特定的数据存储

### 52. 事务的ACID是指什么？（绝对重点）

答： 

- 原子性(Atomic)：事务中各项操作，要么全做要么全不做，任何一项操作的失败都会导致整个事务的失败； 
- 一致性(Consistent)：事务结束后系统状态是一致的； 
- 隔离性(Isolated)：并发执行的事务彼此无法看到对方的中间状态； 
- 持久性(Durable)：事务完成后所做的改动都会被持久化，即使发生灾难性的失败。通过日志和同步备份可以在故障发生后重建数据

脏读（Dirty Read）：A事务读取B事务尚未提交的数据并在此基础上操作，而B事务执行回滚，那么A读取到的数据就是脏数据

| 时间 | 转账事务A                   | 取款事务B                |
| ---- | --------------------------- | ------------------------ |
| T1   |                             | 开始事务                 |
| T2   | 开始事务                    |                          |
| T3   |                             | 查询账户余额为1000元     |
| T4   |                             | 取出500元余额修改为500元 |
| T5   | 查询账户余额为500元（脏读） |                          |
| T6   |                             | 撤销事务余额恢复为1000元 |
| T7   | 汇入100元把余额修改为600元  |                          |
| T8   | 提交事务                    |                          |

不可重复读（Unrepeatable Read）：事务A重新读取前面读取过的数据，发现该数据已经被另一个已提交的事务B修改过了（相同数据同一个事务多次读取不同）

| 时间 | 转账事务A                         | 取款事务B                |
| ---- | --------------------------------- | ------------------------ |
| T1   |                                   | 开始事务                 |
| T2   | 开始事务                          |                          |
| T3   |                                   | 查询账户余额为1000元     |
| T4   | 查询账户余额为1000元              |                          |
| T5   |                                   | 取出100元修改余额为900元 |
| T6   |                                   | 提交事务                 |
| T7   | 查询账户余额为900元（不可重复读） |                          |

幻读（Phantom Read）：事务A重新执行一个查询，返回一系列符合查询条件的行，发现其中插入了被事务B提交的行（一个事务多次对数据操作，读取到另一事务新增或删除的行数据）

| 时间 | 统计金额事务A                   | 转账事务B                 |
| ---- | ------------------------------- | ------------------------- |
| T1   |                                 | 开始事务                  |
| T2   | 开始事务                        |                           |
| T3   | 统计总存款为10000元             |                           |
| T4   |                                 | 新增一个存款账户存入100元 |
| T5   |                                 | 提交事务                  |
| T6   | 再次统计总存款为10100元（幻读） |                           |

- ```
  大致的区别在于不可重复读是由于另一个事务对数据的更改所造成的，而幻读是由于另一个事务插入或删除引起的
  ```

数据库事务的隔离级别有4种，由低到高分别为Read uncommitted 、Read committed 、Repeatable read 、Serializable

![1527563615793](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527563615793.png)

- 七种事务的传播机制（说出最关键的两种即可）

  **1： PROPAGATION_REQUIRED**(加入事务不用新起事务和任意异常总体回滚）

  **例如：**

  ​        ServiceB.methodB的事务级别定义为PROPAGATION_REQUIRED，ServiceA.methodA已经起了事务，这时调用ServiceB.methodB，ServiceB.methodB就加入ServiceA.methodA的事务内部，就不再起新的事务。ServiceA.methodA没有在事务中，这时调用ServiceB.methodB，ServiceB.methodB就会为自己分配一个事务。

  ​        ==在ServiceA.methodA或者在ServiceB.methodB内的任何地方出现异常，事务都会被回滚==。即使ServiceB.methodB的事务已经被提交，但是ServiceA.methodA在接下来fail要回滚，ServiceB.methodB也要回滚

  **2： PROPAGATION_REQUIRES_NEW**（新起事务和出现异常独立回滚）

  **例如**：

  ​        ServiceA.methodA的事务级别为PROPAGATION_REQUIRED，ServiceB.methodB的事务级别为PROPAGATION_REQUIRES_NEW，当调用ServiceB.methodB的时候，ServiceA.methodA所在的事务就会挂起，ServiceB.methodB会起一个新的事务，等待ServiceB.methodB的事务完成以后，他才继续执行。

  ​        ==PROPAGATION_REQUIRES_NEW与PROPAGATION_REQUIRED 的事务区别在于事务的回滚程度：==

  ​	因为ServiceB.methodB和ServiceA.methodA两个不同的事务。如果ServiceB.methodB已经提交，那么ServiceA.methodA失败回滚，ServiceB.methodB是不会回滚的。如果ServiceB.methodB失败回滚，如果他抛出的异常被ServiceA.methodA捕获，ServiceA.methodA事务仍然可能提交

### 53. 获得一个类的类对象有哪些方式？

答： 

- 方法1：类型.class，例如：String.class 
- 方法2：对象.getClass()，例如："hello".getClass() 
- 方法3：Class.forName()，例如：Class.forName("java.lang.String")

### 54. 如何通过反射创建对象？

答： 

- 方法1：通过类对象调用newInstance()方法，例如：String.class.newInstance() 
- 方法2：通过类对象的getConstructor()或getDeclaredConstructor()方法获得构造器（Constructor）对象并调用其newInstance()方法创建对象，例如：String.class.getConstructor(String.class).newInstance("Hello")

### 55. 如何通过反射获取和设置对象私有字段的值？

答：可以通过类对象的getDeclaredField()方法字段（Field）对象，然后再通过字段对象的setAccessible(true)将其设置为可以访问，接下来就可以通过get/set方法来获取/设置字段的值了

### 56. 如何通过反射调用对象的方法？

```java
import java.lang.reflect.Method;

class MethodInvokeTest {

    public static void main(String[] args) throws Exception {
        String str = "hello";
        Method m = str.getClass().getMethod("toUpperCase");
        System.out.println(m.invoke(str));  // HELLO
    }
}
```

### 57. 简述一下面向对象的"六原则一法则"（强烈建议和面向对象结合起来回答，逼格太高）

- 单一职责原则：一个类只做它该做的事情。（单一职责原则想表达的就是"高内聚"，写代码最终极的原则只有六个字"高内聚、低耦合"，所谓的高内聚就是一个代码模块只完成一项功能

- 开闭原则：软件实体应当对扩展开放，对修改关闭，关键就是抽象，一个系统中如果没有抽象类或接口系统就没有扩展点

- 依赖倒转原则：面向接口编程

- 里氏替换原则：任何时候都可以用子类型替换掉父类型，简单的说就是能用父类型的地方就一定能使用子类型，需要注意的是：子类一定是增加父类的能力而不是减少父类的能力，因为子类比父类的能力更多，把能力多的对象当成能力少的对象来用当然没有任何问题

- 接口隔离原则：接口要小而专，绝不能大而全；Java中的接口代表能力、代表约定，能否正确的使用接口一定是编程水平高低的重要标识

- 合成聚合复用原则：优先使用聚合或合成关系复用代码（类与类之间简单的说有三种关系，Is-A关系、Has-A关系、Use-A关系，分别代表继承、关联和依赖），合成聚合复用原则想表达的是优先考虑Has-A关系而不是Is-A关系复用代码；记住：任何时候都不要继承工具类，工具是可以拥有并可以使用的，而不是拿来继承的

- 迪米特法则：简单的说就是如何做到"低耦合"，门面模式和调停者模式就是对迪米特法则的践行

  - 门面模式：你去一家公司洽谈业务，你不需要了解这个公司内部是如何运作的，你甚至可以对这个公司一无所知，去的时候只需要找到公司入口处的前台美女，告诉她们你要做什么，她们会找到合适的人跟你接洽，前台的美女就是公司这个系统的门面；Java Web开发中作为前端控制器的Servlet或Filter不就是一个门面吗

  - 调停者模式：主板作为一个调停者的身份出现，它将各个设备连接在一起而不需要每个设备之间直接交换数据，这样就减小了系统的耦合度和复杂度

    ![1527580723930](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527580723930.png)


### 58. 简述一下你了解的设计模式（绝对重点）

答：GoF提出了三类（创建型[对类的实例化过程的抽象化]、结构型[描述如何将类或对象结合在一起形成更大的结构]、行为型[对在不同的对象之间划分责任和算法的抽象化]）共23种设计模式

- 工厂模式：工厂类可以根据条件生成不同的子类实例，这些子类有一个公共的抽象父类并且实现了相同的方法，但是这些方法针对不同的数据进行了不同的操作（多态方法）。当得到子类的实例后，开发人员可以调用基类中的方法而不必考虑到底返回的是哪一个子类的实例
- 代理模式：给一个对象提供一个代理对象，并由代理对象控制原对象的引用（AOP）---静态代理，动态代理（JDK代理），cglib代理
- 模板方法模式：提供一个抽象类，将部分逻辑以具体方法或构造器的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法（多态实现），从而实现不同的业务逻辑

### 59. 高并发下的单例类书写(绝对重点)

　　1、单例类只能有一个实例。
　　2、单例类必须自己创建自己的唯一实例。
　　3、单例类必须给所有其他对象提供这一实例。

**意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。

**主要解决：**一个全局使用的类频繁地创建与销毁。

**应用场景举例：**单例对象通常作为程序中的存放配置信息的载体，因为它能保证其他对象读到一致的信息。例如在某个服务器程序中，该服务器的配置信息可能存放在数据库或文件中，这些配置数据由某个单例对象统一读取，服务进程中的其他对象如果要获取这些配置信息，只需访问该单例对象即可。这种方式极大地简化了在复杂环境下，尤其是多线程环境下的配置管理

#### **Double Check Locking** 双检查锁机制（推荐）

```java
public class MySingleton {  
      
    //使用volatile关键字保其可见性  
    volatile private static MySingleton instance = null;  
      
    private MySingleton(){}  
       
    public static MySingleton getInstance() {  
        try {    
            if(instance != null){//懒汉式   
                  
            }else{  
                //创建实例之前可能会有一些准备性的耗时工作   
                Thread.sleep(300);  
                synchronized (MySingleton.class) {  
                    if(instance == null){//二次检查  
                        instance = new MySingleton();  
                    }  
                }  
            }   
        } catch (InterruptedException e) {   
            e.printStackTrace();  
        }  
        return instance;  
    }  
}  
```

#### 使用静态内置类实现单例模式

```java
public class MySingleton {  
      
    //内部类  
    private static class MySingletonHandler{  
        private static MySingleton instance = new MySingleton();  
    }   
      
    private MySingleton(){}  
       
    public static MySingleton getInstance() {   
        return MySingletonHandler.instance;  
    }  
}  
```

##### **注意：静态内部类虽然保证了单例在多线程并发下的线程安全性，但是在遇到序列化对象时，默认的方式运行得到的结果就是多例的**

#### 完整版使用静态内置类实现单例模式(**重要**)

```java
public class MySingleton implements Serializable {  
       
    private static final long serialVersionUID = 1L;  
  
    //内部类  
    private static class MySingletonHandler{  
        private static MySingleton instance = new MySingleton();  
    }   
      
    private MySingleton(){}  
       
    public static MySingleton getInstance() {   
        return MySingletonHandler.instance;  
    }  
      
    //该方法在反序列化时会被调用，该方法不是接口定义的方法，有点儿约定俗成的感觉  
    protected Object readResolve() throws ObjectStreamException {  
        System.out.println("调用了readResolve方法！");  
        return MySingletonHandler.instance;   
    }  
}  
```

​

### 60. 排序和二分查找略



## 二、JAVA WEB 和 WEB SERVICE

### 1. Servlet接口中有哪些方法？

答：Servlet接口定义了5个方法，其中前三个方法与Servlet生命周期相关： 
\- void init(ServletConfig config) throws ServletException 
\- void service(ServletRequest req, ServletResponse resp) throws ServletException, java.io.IOException 
\- void destory() 
\- java.lang.String getServletInfo() 
\- ServletConfig getServletConfig()

Web容器加载Servlet并将其实例化后，Servlet生命周期开始，容器运行其init()方法进行Servlet的初始化；请求到达时调用Servlet的service()方法，service()方法会根据需要调用与请求对应的doGet或doPost等方法；当服务器关闭或项目被卸载时服务器会将Servlet实例销毁，此时会调用Servlet的destroy()方法

### 2. 转发（forward）和重定向（redirect）的区别？Servlet属于单例+多线程安全问题（重点）

IE地址是否变化？转发不会，重定向会

参数可否取得？转发可获得，重定向无法获取

发送了几次请求？转发一次，重定向两次

servlet单例，后续都是同一个对象问我们服务，所以写servlet的时候不要定义成员变量，防止线程安全问题

### 3. JSP有哪些内置对象？作用分别是什么？

答：JSP有9个内置对象： 

- request：封装客户端的请求，其中包含来自GET或POST请求的参数； 

- response：封装服务器对客户端的响应； 

- pageContext：通过该对象可以获取其他对象； 

- session：封装用户会话的对象； 

- application：封装服务器运行环境的对象； 

- out：输出服务器响应的输出流对象； 

- config：Web应用的配置对象； 

- page：JSP页面本身（相当于Java程序中的this）； 

- exception：封装页面抛出异常的对象

  jsp详解：

  每个JSP页面都是一个Servlet，第一次请求一个JSP页面时，Servlet/JSP容器首先将JSP页面转换成一个JSP页面的实现类，转换成功后，容器会编译Servlet类，之后容器加载和实例化Java字节码，并执行它通常对Servlet所做的生命周期操作。对同一个JSP页面的后续请求，容器会查看这个JSP页面是否被修改过，如果修改过就会重新转换并重新编译并执行。如果没有则执行内存中已经存在的Servlet实例

  ==Servlet和JSP最主要的不同点在于==，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件

### 4. get和post请求的区别？

答： 
①get请求用来从服务器上获得资源，而post是用来向服务器提交数据； 
②get将表单中数据按照name=value的形式，添加到action 所指向的URL 后面，并且两者使用"?"连接，而各个变量之间使用"&"连接；post是将表单中的数据放在HTTP协议的请求头或消息体中，传递到action所指向URL； 
③get传输的数据要受到URL长度限制（1024字节）；而post可以传输大量的数据，上传文件通常要使用post方式； 
④使用get时参数会显示在地址栏上，如果这些数据不是敏感数据，那么可以使用get；对于敏感数据还是应用使用post； 
⑤get使用MIME类型application/x-www-form-urlencoded的URL编码（也叫百分号编码）文本的格式传递参数，保证被传送的参数由遵循规范的文本组成，例如一个空格的编码是"%20"。

### 5. 讲解JSP中的四种作用域

- page代表与一个页面相关的对象和属性。 
- request代表与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件；需要在页面显示的临时数据可以置于此作用域。 
- session代表与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的session中。 
- application代表与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域

### 6. 实现会话跟踪的技术有哪些？（重点）

1. cookie：cookie有两种，一种是基于窗口的，浏览器窗口关闭后，cookie就没有了；另一种是将信息存储在一个临时文件中，并设置存在的时间。当用户通过浏览器和服务器建立一次会话后，会话ID就会随响应信息返回存储在基于窗口的cookie中，那就意味着只要浏览器没有关闭，会话没有超时，下一次请求时这个会话ID又会提交给服务器让服务器识别用户身份

2. HttpSession：在所有会话跟踪技术中，HttpSession对象是最强大也是功能最多的。当一个用户第一次访问某个网站时会自动创建HttpSession，每个用户可以访问他自己的HttpSession，HttpSession通常放在服务端，每个session都有一个唯一标识，这个标识需要通过cookie放到客户端，下次访问带上方便服务器判断客户端的身份

3. 客户端对session的支持：- 通过cookie保存sessionId；- 通过url的参数携带sessionId；- 通过表单的隐藏字段携带sessionId

   添加到HttpSession中的值可以是任意Java对象，这个对象最好实现了Serializable接口，这样Servlet容器在必要的时候可以将其序列化到文件中，否则在序列化时就会出现异常

   > 补充：session的销毁有两种情况：1). session超时（可以在web.xml中通过<session-config>/<session-timeout>标签配置超时时间）；2). 通过调用session对象的invalidate()方法使session失效

### 7. Servlet 3中的异步处理指的是什么？

**如果一个任务处理时间相当长，那么Servlet或Filter会一直占用着请求处理线程直到任务结束，随着并发用户的增加，容器将会遭遇线程超出的风险，这这种情况下很多的请求将会被堆积起来而后续的请求可能会遭遇拒绝服务，直到有资源可以处理请求为止。异步特性可以帮助应用节省容器中的线程，特别适合执行时间长而且用户需要得到结果的任务**

### 8. 如何在基于Java的Web项目中实现文件上传和下载？

```html
<%@ page pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Photo Upload</title>
</head>
<body>
<h1>Select your photo and upload</h1>
<hr/>
<div style="color:red;font-size:14px;">${hint}</div>
<form action="UploadServlet" method="post" enctype="multipart/form-data">
    Photo file: <input type="file" name="photo" />
    <input type="submit" value="Upload" />
</form>
</body>
</html>
```

```java
@WebServlet("/UploadServlet")
@MultipartConfig
public class UploadServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request,
            HttpServletResponse response) throws ServletException, IOException {
        // 可以用request.getPart()方法获得名为photo的上传附件
        // 也可以用request.getParts()获得所有上传附件（多文件上传）
        // 然后通过循环分别处理每一个上传的文件
        Part part = request.getPart("photo");
        if (part != null && part.getSubmittedFileName().length() > 0) {
            // 用ServletContext对象的getRealPath()方法获得上传文件夹的绝对路径
            String savePath = request.getServletContext().getRealPath("/upload");
            // Servlet 3.1规范中可以用Part对象的getSubmittedFileName()方法获得上传的文件名
            // 更好的做法是为上传的文件进行重命名（避免同名文件的相互覆盖）
            part.write(savePath + "/" + part.getSubmittedFileName());
            request.setAttribute("hint", "Upload Successfully!");
        } else {
            request.setAttribute("hint", "Upload failed!");
        }
        // 跳转回到上传页面
        request.getRequestDispatcher("index.jsp").forward(request, response);
    }

}
```



### 9. 什么是Web Service（Web服务）？

答：Web Service就是一个应用程序，它向外界暴露出一个能够通过Web进行调用的API，它基于HTTP协议传输数据，这使得运行在不同机器上的不同应用无须借助附加的、专门的第三方软件或硬件，就可相互交换数据或集成

### 10. 概念解释：SOAP、WSDL、UDDI

- SOAP：简单对象访问协议（Simple Object Access Protocol），是Web Service中交换数据的一种协议规范
- WSDL：Web服务描述语言（Web Service Description Language），它描述了Web服务的公共接口
- UDDI：统一描述、发现和集成（Universal Description, Discovery and Integration），它是一个基于XML的跨平台的描述规范，是访问各种WSDL的一个门面

### 11. Java规范中和Web Service相关的规范有哪些？

> ==JAX-RS==(JSR 311 & JSR 339 & JSR 370)：是Java针对REST（Representation State Transfer）架构风格制定的一套Web Service规范。REST是一种软件架构模式，是一种风格，它不像SOAP那样本身承载着一种消息协议， (两种风格的Web Service均采用了HTTP做传输协议，因为HTTP协议能穿越防火墙，Java的远程方法调用（RMI）等是重量级协议，通常不能穿越防火墙），因此可以将REST视为基于HTTP协议的软件架构。==REST中最重要的两个概念是资源定位和资源操作==，而HTTP协议恰好完整的提供了这两个点。==HTTP协议中的URI可以完成资源定位，而GET、POST、OPTION、DELETE方法可以完成资源操作==。==因此REST完全依赖HTTP协议就可以完成Web Service，而不像SOAP协议那样只利用了HTTP的传输特性，定位和操作都是由SOAP协议自身完成的，==使得基于SOAP的Web Service显得笨重而逐渐被淘汰

总结：REST和HTTP真的是相得益彰，REST是设计风格，HPPT是传输协议，通过HTTP就可以实现REST风格并由后台服务器解析（资源定位和资源操作），然后提供服务



## 三. JAVA 框架

### 1. 什么是ORM？（重点） 

答：对象关系映射（Object-Relational Mapping，简称ORM），通过使用描述对象和数据库之间映射的元数据（在Java中可以用XML或者是注解），将程序中的对象自动持久化到关系数据库中或者将关系数据库表中的行转换成Java对象，其本质上就是将数据从一种形式转换到另外一种形式

### 2. 持久层设计要考虑的问题有哪些？

答：持久层就是系统中专注于实现数据持久化的相对独立的层面

==持久层设计的目标包括：== 

- 数据存储逻辑的分离，提供抽象化的数据访问接口。 
- 数据访问底层实现的分离，可以在不修改代码的情况下切换底层实现。 
- 资源管理和调度的分离，在数据访问层实现统一的资源调度（如缓存机制）

### 3. Hibernate中SessionFactory是线程安全的吗？Session是线程安全的吗（两个线程能够共享同一个Session吗）？（重点）

答：==SessionFactory对应Hibernate的一个数据存储的概念，它是线程安全的，可以被多个线程并发访问==。

SessionFactory一般只会在启动的时候构建。对于应用程序，最好将SessionFactory通过单例模式进行封装以便于访问。

==Session是一个轻量级非线程安全的对象（线程间不能共享session）==，它表示与数据库进行交互的一个工作单元。Session是由SessionFactory创建的，是持久层服务对外提供的主要接口。Session会延迟获取数据库连接（也就是在需要的时候才会获取）。==为了避免创建太多的session，可以使用ThreadLocal将session和当前线程绑定在一起，这样可以让同一个线程获得的总是同一个session。Hibernate 3中SessionFactory的getCurrentSession()方法就可以做到==。

**注意：线程安全问题都是由全局变量及静态变量引起的。**

**若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，否则的话就可能影响线程安全。**

### 4. Hibernate中Session的load和get方法的区别是什么？

答：主要有以下三项区别： 

1. 如果没有找到符合条件的记录，get方法返回null，load方法抛出异常，对于load()方法Hibernate认为该数据在数据库中一定存在可以放心的使用代理来实现延迟加载，如果没有数据就抛出异常，而通过get()方法获取的数据可以不存在
2. get方法直接返回实体类对象，load方法返回实体类对象的代理。 
3. 从Hibernate 3开始，get方法不再是对二级缓存只写不读，它也是可以访问二级缓存的，同load方法

### 5. 阐述Session加载实体对象的过程（重点）

答：Session加载实体对象的步骤是： 
① Session在调用数据库查询功能之前，首先会在一级缓存中通过实体类型和主键进行查找，如果一级缓存查找命中且数据状态合法，则直接返回； （一级缓存）

② 如果一级缓存没有命中，接下来Session会在当前NonExists记录（相当于一个查询黑名单，如果出现重复的无效查询可以迅速做出判断，从而提升性能）中进行查找，如果NonExists中存在同样的查询条件，则返回null； （查询黑名单）

③ 如果一级缓存查询失败则查询二级缓存，如果二级缓存命中则直接返回； （二级缓存）

④ 如果之前的查询都未命中，则发出SQL语句，如果查询未发现对应记录则将此次查询添加到Session的NonExists中加以记录，并返回null； （发送sql语句）

⑤ 根据映射配置和SQL语句得到ResultSet，并创建对应的实体对象； （创建映射对象）

⑥ 将对象纳入Session（一级缓存）的管理； （一级缓存管理）
⑦ 如果有对应的拦截器，则执行拦截器的onLoad方法； 
⑧ 如果开启并设置了要使用二级缓存，则将数据对象纳入二级缓存； （二级缓存管理，开启的情况下）
⑨ 返回数据对象

### 6. Hibernate如何实现分页查询？

答：通过Hibernate实现分页查询，开发人员只需要提供HQL语句（调用Session的createQuery()方法）或查询条件（调用Session的createCriteria()方法）、设置查询起始行数（调用Query或Criteria接口的setFirstResult()方法）和最大查询行数（调用Query或Criteria接口的setMaxResults()方法），并调用Query或Criteria接口的list()方法，Hibernate会自动生成分页查询的SQL语句

### 7. 简述Hibernate的悲观锁和乐观锁机制（重点）

- 悲观锁，顾名思义悲观的认为在数据处理过程中极有可能存在修改数据的并发事务（包括本系统的其他事务或来自外部系统的事务），==于是将处理的数据设置为锁定状态。悲观锁必须依赖数据库本身的锁机制才能真正保证数据访问的排他性==
- ==乐观锁，顾名思义，对并发事务持乐观态度（认为对数据的并发操作不会经常性的发生），通过更加宽松的锁机制来解决由于悲观锁排他性的数据访问对系统性能造成的严重影响；最常见的乐观锁是通过数据版本标识来实现的，读取数据时获得数据的版本号，更新数据时将此版本号加1，然后和数据库表对应记录的当前版本号进行比较，如果提交的数据版本号大于数据库中此记录的当前版本号则更新数据，否则认为是过期数据无法更新==（允许其他事务对正在更新的数据查询，但同时修改就会有问题）
- 注意：使用乐观锁会增加了一个版本字段，很明显这需要额外的空间来存储这个版本字段，浪费了空间，但是乐观锁会让系统具有更好的并发性，这是对时间的节省。因此乐观锁也是典型的空间换时间的策略

### 8. 阐述实体对象的四种状态以及转换关系（重点）

- 瞬时态：当new一个实体对象后，这个对象处于瞬时态，这个对象所保存的数据与数据库没有任何关系
- 持久态：持久态对象的实例在数据库中有对应的记录，并拥有一个持久化标识（ID）
- 移除态：对持久态对象进行delete操作后，数据库中对应的记录将被删除，那么持久态对象与数据库记录不再存在对应关系，持久态对象变成移除态（可以视为瞬时态）
- 游离态：当Session进行了close()、clear()、evict()或flush()后，实体对象从持久态变成游离态，对象虽然拥有持久和与数据库对应记录一致的标识值，但是因为对象已经从会话中清除掉，对象不在持久化管理之内，所以处于游离态（也叫脱管态）

### 9. 如何理解Hibernate的延迟加载机制？在实际应用中，延迟加载与Session关闭的矛盾是如何处理的？（重点）

答：延迟加载就是并不是在读取的时候就把数据加载进来，而是等到使用时再加载。Hibernate使用了虚拟代理机制实现延迟加载，我们使用Session的load()方法加载数据或者一对多关联映射在使用延迟加载的情况下从一的一方加载多的一方，得到的都是虚拟代理，简单的说返回给用户的并不是实体本身，而是实体对象的代理。代理对象在用户调用getter方法时才会去数据库加载数据。但加载数据就需要数据库连接。

==而当我们把会话关闭时，数据库连接就同时关闭了==

==延迟加载与session关闭的矛盾一般可以这样处理：==

① ==关闭延迟加载特性==。这种方式操作起来比较简单，因为Hibernate的延迟加载特性是可以通过映射文件或者注解进行配置的，但这种解决方案存在明显的缺陷。如果去掉延迟加载的话，每次查询的开销都会变得很大。 
② ==在session关闭之前先获取需要查询的数据==，可以使用工具方法Hibernate.isInitialized()判断对象是否被加载，如果没有被加载则可以使用Hibernate.initialize()方法加载对象。 
③ ==使用拦截器或过滤器延长Session的生命周期直到视图获得数据==。Spring整合Hibernate提供的OpenSessionInViewFilter和OpenSessionInViewInterceptor就是这种做法

### 10. 谈一下你对继承映射的理解

① 每个继承结构一张表（table per class hierarchy），不管多少个子类都用一张表。 
② 每个子类一张表（table per subclass），公共信息放一张表，特有信息放单独的表。 
③ 每个具体类一张表（table per concrete class），有多少个子类就有多少张表。 
第一种方式属于单表策略，其优点在于查询子类对象的时候无需表连接，查询速度快；缺点是可能导致表很大。后两种方式属于多表策略，其优点在于数据存储紧凑，其缺点是需要进行连接查询，速度较慢

### 11. 简述Hibernate常见优化策略（重点）

① 制定合理的缓存策略（二级缓存）

③ 尽量使用延迟加载特性

⑤ 如果可以，选用UUID作为主键生成器

⑥ 如果可以，选用基于版本号的乐观锁替代悲观锁

⑧ 考虑数据库本身的优化，合理的索引、恰当的数据分区策略等都会对持久层的性能带来可观的提升

### 12. 谈一谈Hibernate的一级缓存、二级缓存和查询缓存（重点） 

答：Hibernate的Session提供了一级缓存的功能，默认总是有效的，当应用程序保存持久化实体、修改持久化实体时，==Session并不会立即把这种改变提交到数据库，而是缓存在当前的Session中，除非显示调用了Session的flush()方法或通过close()方法关闭Session==。通过一级缓存，可以减少程序与数据库的交互，从而提高数据库访问性能。 
==SessionFactory级别的二级缓存是全局性的，所有的Session可以共享这个二级缓存==。不过二级缓存默认是关闭的，需要显示开启并指定需要使用哪种二级缓存实现类（可以使用第三方提供的实现）。==一旦开启了二级缓存并设置了需要使用二级缓存的实体类，SessionFactory就会缓存访问过的该实体类的每个对象，除非缓存的数据超出了指定的缓存空间，通常用于很少变动的数据==。 
一级缓存和二级缓存都是对整个实体进行缓存，不会缓存普通属性，如果希望对普通属性进行缓存，可以使用查询缓存。==查询缓存是将HQL或SQL语句以及它们的查询结果作为键值对进行缓存，对于同样的查询可以直接从缓存中获取数据。查询缓存默认也是关闭的，需要显示开启==

### 13. Hibernate中DetachedCriteria类是做什么的？（重点）

DetachedCriteria和Criteria的用法基本上是一致的，但Criteria是由Session的createCriteria()方法创建的，也就意味着离开创建它的Session，Criteria就无法使用了。DetachedCriteria不需要Session就可以创建（使用DetachedCriteria.forClass()方法创建），所以通常也称其为离线的Criteria，在需要进行查询操作的时候再和Session绑定（调用其getExecutableCriteria(Session)方法），这也就意味着一个DetachedCriteria可以在需要的时候和不同的Session进行绑定

- ==QBC 查询就是通过使用 Hibernate 提供的 Query By Criteria API 来查询对象，这种 API 封装了 SQL 语句的动态拼装，对查询提供了更加面向对象的功能接口==（简单说，QBC就是通过不同的逻辑方法由hibernate为我们自动拼接sql语句）

  ```java
  	/*
  	 * 使用QBC进行排序和分页查询
  	 */
  	@Test
  	void testQBC4() {
  		
  		Criteria criteria = this.session.createCriteria(Employee.class);
  		
  		//1. 添加排序
  		criteria.addOrder(Order.asc("salary"));
  		criteria.addOrder(Order.desc("id"));
  		
  		//2. 添加分页
  		int pageNo = 1;
  		int pageSize = 2;
  		criteria.setFirstResult((pageNo-1) * pageSize).setMaxResults(pageSize);
  		criteria.list();
  	}
  ```

### 14. @OneToMany注解的mappedBy属性有什么作用？(重点)

@OneToMany用来配置一对多关联映射，但通常情况下，一对多关联映射都由多的一方来维护关联关系，例如学生和班级，应该在学生类中添加班级属性来维持学生和班级的关联关系（在数据库中是由学生表中的外键班级编号来维护学生表和班级表的多对一关系）

### 15. MyBatis中使用#和$书写占位符有什么区别？（重点）

答：`#`将传入的数据都当成一个字符串，会对传入的数据自动加上引号；`$`将传入的数据直接显示生成在SQL中。注意：使用`$`占位符可能会导致SQL注射攻击，能用`#`的地方就不要使用`$`，写order by子句的时候应该用`$`而不是`#`（因为如果使用 # 传入字符串格式的排序参数不符合order by 语法，无法排序）

### 16. 解释一下MyBatis中命名空间（namespace）的作用

答：在大型项目中，可能存在大量的SQL语句，这时候为每个SQL语句起一个唯一的标识（ID）就变得并不容易了。为了解决这个问题，在MyBatis中，可以为每个映射文件起一个唯一的命名空间，这样定义在这个映射文件中的每个SQL语句就成了定义在这个命名空间中的一个ID。只要我们能够保证每个命名空间中这个ID是唯一的，即使在不同映射文件中的语句ID相同，也不会再产生冲突了

### 17. MyBatis中的动态SQL是什么意思？（重点）

答：对于一些复杂的查询，我们可能会指定多个查询条件，但是这些条件可能存在也可能不存在，例如在58同城上面找房子，我们可能会指定面积、楼层和所在位置来查找房源，也可能会指定面积、价格、户型和所在位置来查找房源，此时就需要根据用户指定的条件动态生成SQL语句。如果不使用持久层框架我们可能需要自己拼装SQL语句，还好MyBatis提供了动态SQL的功能来解决这个问题。MyBatis中用于实现动态SQL的元素主要有： 

- if （判断，可多个）
- choose / when / otherwise （分支判断）
- trim （自定义拼接sql规则）
- where （条件）
- set （更新）
- foreach（批处理）

CRUD:增加(Create)、读取查询(Retrieve)、更新(Update)和删除(Delete)

```xml
<!-- id：和接口的对应方法绑定 public Employee getById(Integer id); -->	
  <select id="getById" resultType="emp" databaseId="mysql">
		select
		id,name,email,gender from tb_employee where id = #{id}
	</select>

	<!-- 增删该可以定义参数类型 parameterType，但是一般不用定义，并且返回值如果是 Boolean，Integer，Long 类型也无需定义 returnType -->
	<!-- public boolean add(Employee employee); -->
	<!-- 获取自增主键的值 mysql如果使用的是自增主键策略，那么可以通过 statement.getGeneratedKeys() 获取到新增数据的id值; useGeneratedKeys:使用自增主键获取主键值策略 keyProperty:指定对应的javaBean的属性值，即mybatis获取到主键值以后封装给javaBean的哪个属性 -->
	<insert id="add" parameterType="com.zc.cris.mybatis.bean.Employee"
		useGeneratedKeys="true" keyProperty="id" databaseId="mysql">
		insert into
		tb_employee (name,email,gender) values (#{name},#{email},#{gender})
	</insert>

	<!-- public Integer update(Employee employee); -->
	<update id="update">
		update tb_employee
		set name=#{name}, email=#{email}, gender=#{gender}
		where id=#{id}
	</update>

	<!-- public boolean remove(Integer id); -->
	<delete id="remove">
		delete from tb_employee where id=#{id}
	</delete>
```

#### - mysql查询详解（详见我的GitHub即可）

- ##### 如果查询返回值为集合

- ##### 如果查询结果为 map（2种情况）

  - 第一种：map的key 为列名，map的value 为列值
  - 第二种情况（key 为指定的列名，值为封装后的javabean 对象）

- #### 自定义resultMap（重点）

#### - 联合查询

#### - 集合属性的关联查询

#### - 动态sql

### 18. 什么是IoC和DI？DI是如何实现的？（绝对重点，高逼格）

答：IoC叫控制反转，是Inversion of Control的缩写，DI（Dependency Injection）叫依赖注入，是对IoC更简单的诠释。控制反转是把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的"控制反转"就是对组件对象控制权的转移，从程序代码本身转移到了外部容器，由容器来创建对象并管理对象之间的依赖关系

依赖注入可以通过setter方法注入（设值注入）、构造器注入和接口注入三种方式来实现，Spring支持setter注入和构造器注入，通常使用构造器注入来注入必须的依赖关系，对于可选的依赖关系，则setter注入是更好的选择

==个人见解==：以前当我们创建一个javaBean并且需要使用它的时候，通常是需要手工的对其进行初始化，然后调用这个对象的属性和方法，IOC容器的存在，使得我们从繁琐的创建过程中脱离出来，将javaBean或者其他组件的控制权交给了Spring，我们不需要亲自去创建各种需要的组件，只要告诉spring，我们需要什么，那么spring就自动的为我们提供什么

==举例说明==：我们以前买菜都是亲自提着菜篮子去菜市场买菜，突然有一天，菜市场说不需要我们再跑去菜市场了，而是由菜市场亲自为我们送菜，只需要把菜篮子放在家门口，告诉菜市场我们需要一个白菜，那么白菜就会被自动被填充进菜篮子，大大方便我们后续的做菜（开发）流程

#### IOC发展历程（以业务层为例）

1. 接口和实现类：业务层需要一个美女（接口），得自己提供一个柳岩或者佟丽娅的实例
2. 工厂模式：业务层不需要关注实例到底怎么生成的，只需要向工厂需求一个美女，工厂自动给我们提供一个柳岩或者迪丽热巴
3. IOC(反转控制)：业务层需要一个美女，我们不用再写工厂类，直接从IOC容器中已经创建好的实例取就行了，IOC帮我们自动完成业务层属性到IOC容器实例的引用

### 19. Spring中Bean的作用域有哪些？

答：在Spring的早期版本中，仅有两个作用域：singleton和prototype，前者表示Bean以单例的方式存在；后者表示每次从容器中调用Bean时，都会返回一个新的实例，prototype通常翻译为原型。

Spring 2.x中针对WebApplicationContext新增了3个作用域，分别是：request（每次HTTP请求都会创建一个新的Bean）、session（同一个HttpSession共享同一个Bean，不同的HttpSession使用不同的Bean）和globalSession（同一个全局Session共享一个Bean）

### 20. 关于ThreadLocal解决多线程程序的并发问题（绝对重点）

ThreadLocal，顾名思义是线程的一个本地化对象，当工作于多线程中的对象使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程分配一个独立的变量副本，所以每一个线程都可以独立的改变自己的副本，而不影响其他线程所对应的副本。从线程的角度看，这个变量就像是线程的本地变量。

- void set(T value)：设置当前线程的线程局部变量的值。 

- T get()：获得当前线程所对应的线程局部变量的值。 

- void remove()：删除当前线程中线程局部变量的值。

  ThreadLocal是如何做到为每一个线程维护一份独立的变量副本的呢？在ThreadLocal类中有一个Map，键为线程对象，值是其线程对应的变量的副本，自己要模拟实现一个ThreadLocal类其实并不困难，代码如下所示：

  ```java
  public class MyThreadLocal<T> {
      private Map<Thread, T> map = Collections.synchronizedMap(new HashMap<Thread, T>());

      public void set(T newValue) {
          map.put(Thread.currentThread(), newValue);
      }

      public T get() {
          return map.get(Thread.currentThread());
      }

      public void remove() {
          map.remove(Thread.currentThread());
      }
  }
  ```

  ![1527683958223](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527683958223.png)

  ​

### 21. 解释一下什么叫AOP（面向切面编程）？

答：AOP（Aspect-Oriented Programming）指一种程序设计模式，该模式以一种称为切面（aspect）的语言构造为基础，切面是一种新的模块化机制，用来描述分散在对象、类或方法中的横切关注点（crosscutting concern）

aop的核心就是动态代理：使用一个代理将对象包装起来, 然后用该代理对象取代原始对象. 任何对原始对象的调用都要通过代理. 代理对象决定是否以及何时将方法调用转到原始对象上

面向切面编程主要解决了以下问题：

1. 代码混乱和膨胀：越来越多的非业务需求(日志和验证等)加入后, 原有的业务方法急剧膨胀. 每个方法在处理核心逻辑的同时还必须兼顾其他多个关注点.
2. 重复代码分散维护困难: 以日志需求为例, 只是为了满足这个单一需求, 就不得不在多个模块（方法）里多次重复相同的日志代码. 如果日志需求发生变化, 必须修改所有模块

### 22. 你是如何理解"横切关注"这个概念的？

答："横切关注"是会影响到整个应用程序的关注功能，它跟正常的业务逻辑是正交的，没有必然的联系，但是几乎所有的业务逻辑都会涉及到这些关注功能。通常，事务、日志、安全性等关注就是应用中的横切关注功能

### 23. 你如何理解AOP中的连接点（Joinpoint）、切点（Pointcut）、增强（Advice）、引介（Introduction）、织入（Weaving）、切面（Aspect）这些概念？（绝对重点）

a. 连接点（Joinpoint）：程序执行的某个特定位置（如：某个方法调用前、调用后，方法抛出异常后）。一个类或一段程序代码拥有一些具有边界性质的特定点，这些代码中的特定点就是连接点。Spring仅支持方法的连接点。 
b. 切点（Pointcut）：如果连接点相当于数据中的记录，那么切点相当于查询条件，一个切点可以匹配多个连接点。Spring AOP的规则解析引擎负责解析切点所设定的查询条件，找到对应的连接点。 
c. 增强（Advice）：增强是织入到目标类连接点上的一段程序代码。

d. 引介（Introduction）：引介是一种特殊的增强，它为类添加一些属性和方法。这样，即使一个业务类原本没有实现某个接口，通过引介功能，可以动态的未该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。 

e. 织入（Weaving）：织入是将增强添加到目标类具体连接点上的过程，AOP有三种织入方式：①编译期织入：需要特殊的Java编译期（例如AspectJ的ajc）；②装载期织入：要求使用特殊的类加载器，在装载类的时候对类进行增强；③运行时织入：在运行时为目标类生成代理实现增强。Spring采用了动态代理的方式实现了运行时织入，而AspectJ采用了编译期织入和装载期织入的方式。 
f. 切面（Aspect）：切面是由切点和增强（引介）组成的，它包括了对横切关注功能的定义，也包括了对连接点的定义

> **补充：**代理模式是GoF提出的23种设计模式中最为经典的模式之一，代理模式是对象的结构模式，它给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用。简单的说，代理对象可以完成比原对象更多的职责，当需要为原对象添加横切关注功能时，就可以使用原对象的代理对象。我们在打开Office系列的Word文档时，如果文档中有插图，当文档刚加载时，文档中的插图都只是一个虚框占位符，等用户真正翻到某页要查看该图片时，才会真正加载这张图，这其实就是对代理模式的使用，代替真正图片的虚框就是一个虚拟代理；Hibernate的load方法也是返回一个虚拟代理对象，等用户真正需要访问对象的属性时，才向数据库发出SQL语句获得真实对象

> **说明：**使用Java的动态代理有一个局限性就是代理的类必须要实现接口，虽然面向接口编程是每个优秀的Java程序都知道的规则，但现实往往不尽如人意，对于没有实现接口的类如何为其生成代理呢？继承！继承是最经典的扩展已有代码能力的手段，虽然继承常常被初学者滥用，但继承也常常被进阶的程序员忽视。CGLib采用非常底层的字节码生成技术，通过为一个类创建子类来生成代理，它弥补了Java动态代理的不足，因此Spring中动态代理和CGLib都是创建代理的重要手段，对于实现了接口的类就用动态代理为其生成代理类，而没有实现接口的类就用CGLib通过继承的方式为其创建代理

### 24. Spring中自动装配的方式有哪些？

- no：不进行自动装配，手动设置Bean的依赖关系。 
- byName：根据Bean的名字进行自动装配。 
- byType：根据Bean的类型进行自动装配。 
- constructor：类似于byType，不过是应用于构造器的参数，如果正好有一个Bean与构造器的参数类型相同则可以自动装配，否则会导致错误。 
- autodetect：如果有默认的构造器，则通过constructor的方式进行自动装配，否则使用byType的方式进行自动装配

### 25. Spring支持的事务管理类型有哪些？你在项目中使用哪种方式？（绝对重点）

答：==Spring支持编程式事务管理和声明式事务管理。许多Spring框架的用户选择声明式事务管理，因为这种方式和应用程序的关联较少，因此更加符合轻量级容器的概念==。声明式事务管理要优于编程式事务管理，尽管在灵活性方面它弱于编程式事务管理，因为编程式事务允许你通过代码控制业务

- 编程式事务

![1527732102129](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527732102129.png)



- 声明式事务

  ```xml
  	<!-- 配置事务管理器 -->
  	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  		<property name="dataSource" ref="dataSource"></property>
  	</bean>

  	<!-- 启用事务注解 -->
  	<tx:annotation-driven transaction-manager="transactionManager"/>
  ```

  ​

事务分为全局事务和局部事务。全局事务由应用服务器管理，需要底层服务器JTA支持（如WebLogic、WildFly等）。局部事务和底层采用的持久化方案有关，例如使用JDBC进行持久化时，需要使用Connetion对象来操作事务；而采用Hibernate进行持久化时，需要使用Session对象来操作事务

**注意：**事务是AOP非常经典的一种实现，AOP是一种概念，事务是其中的一种实现，Spring为事务提供了编程式实现（配置文件里面针对事务方法具体的配置）和声明式实现（和hibernate结合就是注入hibernate的会话工厂，生成transactionManager 事务管理器，需要事务的地方使用@Transactional 注解）

##### AspectJ：一个AOP实现框架，可以快速的向指定方法动态织入我们自定义的代码（或者使用xml配置实现，不建议）

### 26. Spring MVC的工作原理是怎样的（绝对重点，直接画图高逼格）？

![1527730366762](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527730366762.png)

==当 springMVC 调用拦截器 的preHandle 方法的时候，一旦有一个拦截器返回了 false，那么后续的所有拦截器和目标方法都不会执行，而返回 false 的拦截器之前的所有拦截器会执行完 afterCompletion 方法==

① 客户端的所有请求都交给前端控制器DispatcherServlet来处理，它会负责调用系统的其他模块来真正处理用户的请求。 
② DispatcherServlet收到请求后，将根据请求的信息（包括URL、HTTP协议方法、请求头、请求参数、Cookie等）以及HandlerMapping的配置找到处理该请求的Handler（任何一个对象都可以作为请求的Handler）。 
③ 在这个地方Spring会通过HandlerAdapter对该处理器以及拦截器栈（HandlerExecutionChain）里的拦截器进行封装。 
④ HandlerAdapter是一个适配器，链式调用所有拦截器然后以及Handler中的方法进行。 

![1527730957870](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527730957870.png)

⑤ ==Handler完成对用户请求的处理后，会返回一个ModelAndView对象给DispatcherServlet==，ModelAndView顾名思义，包含了数据模型以及相应的视图的信息。 
⑥ ModelAndView的视图是逻辑视图，==DispatcherServlet还要借助ViewResolver完成从逻辑视图到真实视图对象的解析工作==。 
⑦ 当得到真正的视图对象后，DispatcherServlet会利用视图对象对模型数据进行渲染。 
⑧ 客户端得到响应，可能是一个普通的HTML页面，也可以是XML或JSON字符串，还可以是一张图片或者一个PDF文件

### 27. 选择使用Spring框架的原因（Spring框架为企业级开发带来的好处有哪些）？

- 非侵入式：支持基于POJO的编程模式，不强制性的要求实现Spring框架中的接口或继承Spring框架中的类,不会污染我们自己的代码。 
- IoC容器：IoC容器帮助应用程序管理对象以及对象之间的依赖关系，对象之间的依赖关系如果发生了改变只需要修改配置文件而不是修改代码，因为代码的修改可能意味着项目的重新构建和完整的回归测试。有了IoC容器，程序员再也不需要自己编写工厂、单例，这一点特别符合Spring的精神"不要重复的发明轮子"。 
- AOP（面向切面编程）：将所有的横切关注功能封装到切面（aspect）<切点和增强>中，通过配置的方式将横切关注功能动态添加到目标代码上，进一步实现了业务逻辑和系统服务之间的分离。另一方面，有了AOP程序员可以省去很多自己写代理类的工作。 
- MVC：Spring的MVC框架是非常优秀的，从各个方面都可以甩Struts 2几条街，为Web表示层提供了更好的解决方案。 
- 事务管理：==Spring以宽广的胸怀接纳多种持久层技术==，并且为其提供了声明式的事务管理，在不需要任何一行代码的情况下就能够完成事务管理。 
- 其他：选择Spring框架的原因还远不止于此，==Spring为Java企业级开发提供了一站式选择，你可以在需要的时候使用它的部分和全部，更重要的是，你甚至可以在感觉不到Spring存在的情况下，在你的项目中使用Spring提供的各种优秀的功能==。

### 28. Spring IoC容器配置Bean的方式？

IoC容器装配Bean（xml配置方式和注解方式）

- **XML配置方式**

  1. 使用类构造器实例化(默认无参数)
  2. 使用静态工厂方法实例化
  3. 使用实例工厂方法实例化

- **注解方式**

  - Component  描述Spring框架中Bean
  - @Repository 用于对DAO实现类进行标注
  - @Service 用于对Service实现类进行标注
  - @Controller 用于对Controller实现类进行标注

- ==基于Java程序进行配置（Spring 3+）==

  这种配置方式更加简单，不再需要XML文件，看看下面的例子，我们新创建一个类JavaConfigPerson，类中有两个方法person和regiona。和普通类有区别的地方在于多了@Configuration和@Bean两个注解，@Configuration就相当于xml中步骤A里面定义的配置规范，是不是一下少了好多行。@Bean就相当于xml中声明Bean。类中的方法名regiona、person就相当于xml配置中的id，只是它们也可以当作普通方法来使用

  ```java
  @Configuration
  public class JavaConfigPerson {
         @Bean
         public Person person (){

                Person person =  new Person(regiona (),"a funny man");
                person.setName("liming");
                person.setAge("19");
                return person;
         }

         @Bean
         public Region regiona (){
                Region region = new Region();
                region.setProvince("beijing");
                region.setCity("haidian");
                return region;
         }
  }

  - 获取bean
  ApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfigPerson.class);
  Person person = (Person) ctx.getBean("getPerson");
  ```

### 29. 阐述Spring框架中Bean的生命周期？

### 30. 大型网站在架构上应当考虑哪些问题？（绝对重点）

1. 分层：分层是处理任何复杂系统最常见的手段之一，将系统横向切分成若干个层面，每个层面只承担单一的职责，然后通过下层为上层提供的基础设施和服务以及上层对下层的调用来形成一个完整的复杂的系统。大型网站的软件系统也可以使用分层的理念将其分为持久层（提供数据存储和访问服务）、业务层（处理业务逻辑，系统中最核心的部分）和表示层（系统交互、视图展示）。需要指出的是：（1）分层是逻辑上的划分，在物理上可以位于同一设备上也可以在不同的设备上部署不同的功能模块，这样可以使用更多的计算资源来应对用户的并发访问；（2）层与层之间应当有清晰的边界，这样分层才有意义，才更利于软件的开发和维护
2. 分割：分割是对软件的纵向切分。我们可以将大型网站的不同功能和服务分割开，形成高内聚低耦合的功能模块（单元）
3. 分布式：除了上面提到的内容，网站的静态资源（JavaScript、CSS、图片等）也可以采用独立分布式部署并采用独立的域名，这样可以减轻应用服务器的负载压力，也使得浏览器对资源的加载更快。数据的存取也应该是分布式的，传统的商业级关系型数据库产品基本上都支持分布式部署，而新生的NoSQL产品几乎都是分布式的。当然，网站后台的业务处理也要使用分布式技术，例如查询索引的构建、数据分析等，这些业务计算规模庞大，可以使用Hadoop以及MapReduce分布式计算框架来处理
4. 集群：集群使得有更多的服务器提供相同的服务，可以更好的提供对并发的支持
5. 缓存：所谓缓存就是用空间换取时间的技术，将数据尽可能放在距离计算最近的位置。使用缓存是网站优化的第一定律。我们通常说的CDN、反向代理、热点数据都是对缓存技术的使用
6. 异步：异步是实现软件实体之间解耦合的又一重要手段。异步架构是典型的生产者消费者模式，二者之间没有直接的调用关系，只要保持数据结构不变，彼此功能实现可以随意变化而不互相影响，这对网站的扩展非常有利。使用异步处理还可以提高系统可用性，加快网站的响应速度（用Ajax加载数据就是一种异步技术），同时还可以起到削峰作用（应对瞬时高并发）。能推迟处理的都要推迟处理"是网站优化的第二定律，而异步是践行网站优化第二定律的重要手段
7. 冗余：各种服务器都要提供相应的冗余服务器以便在某台或某些服务器宕机时还能保证网站可以正常工作，同时也提供了灾难恢复的可能性。冗余是网站高可用性的重要保证

### 31. 你用过的网站前端优化的技术有哪些？

① 浏览器访问优化： 

- 减少HTTP请求数量：合并CSS、合并JavaScript、合并图片（CSS Sprite） 
- 使用浏览器缓存：通过设置HTTP响应头中的Cache-Control和Expires属性，将CSS、JavaScript、图片等在浏览器中缓存，当这些静态资源需要更新时，可以更新HTML文件中的引用来让浏览器重新请求新的资源 
- 启用压缩 
- CSS前置，JavaScript后置 
- 减少Cookie传输 

② CDN加速：CDN（Content Distribute Network）的本质仍然是缓存，将数据缓存在离用户最近的地方，CDN通常部署在网络运营商的机房，不仅可以提升响应速度，还可以减少应用服务器的压力。当然，CDN缓存的通常都是静态资源。 
③ 反向代理：反向代理相当于应用服务器的一个门面，可以保护网站的安全性，也可以实现负载均衡的功能，当然最重要的是它缓存了用户访问的热点资源，可以直接从反向代理将某些内容返回给用户浏览器

### 32. 使用的服务器优化技术有哪些？（绝对重点）

① 分布式缓存：缓存的本质就是内存中的哈希表，如果设计一个优质的哈希函数，那么理论上哈希表读写的渐近时间复杂度为O(1)。缓存主要用来存放那些读写比很高、变化很少的数据，这样应用程序读取数据时先到缓存中读取，如果没有或者数据已经失效再去访问数据库或文件系统，并根据拟定的规则将数据写入缓存。对网站数据的访问也符合二八定律（Pareto分布，幂律分布），即80%的访问都集中在20%的数据上，如果能够将这20%的数据缓存起来，那么系统的性能将得到显著的改善。当然，使用缓存需要解决以下几个问题： 

- 频繁修改的数据； 
- 数据不一致与脏读； 
- 缓存雪崩（可以采用分布式缓存服务器集群加以解决，[memcached](http://memcached.org/)是广泛采用的解决方案）； 
- 缓存预热； 
- 缓存穿透（恶意持续请求不存在的数据）。 

② 异步操作：可以使用消息队列将调用异步化，通过异步处理将短时间高并发产生的事件消息存储在消息队列中，从而起到削峰作用。电商网站在进行促销活动时，可以将用户的订单请求存入消息队列，这样可以抵御大量的并发订单请求对系统和数据库的冲击。目前，绝大多数的电商网站即便不进行促销活动，订单系统都采用了消息队列来处理。 
③ 使用集群

④ 代码优化： 

- 多线程：基于Java的Web开发基本上都通过多线程的方式响应用户的并发请求，使用多线程技术在编程上要解决线程安全问题，主要可以考虑以下几个方面：A. 将对象设计为无状态对象（这和面向对象的编程观点是矛盾的，在面向对象的世界中被视为不良设计），这样就不会存在并发访问时对象状态不一致的问题。B. 在方法内部创建对象，这样对象由进入方法的线程创建，不会出现多个线程访问同一对象的问题。使用ThreadLocal将对象与线程绑定也是很好的做法，这一点在前面已经探讨过了。C. 对资源进行并发访问时应当使用合理的锁机制。 
- 非阻塞I/O： 使用单线程和非阻塞I/O是目前公认的比多线程的方式更能充分发挥服务器性能的应用模式，基于Node.js构建的服务器就采用了这样的方式。Java在JDK 1.4中就引入了NIO（Non-blocking I/O）,在Servlet 3规范中又引入了异步Servlet的概念，这些都为在服务器端采用非阻塞I/O提供了必要的基础。 
- 资源复用：资源复用主要有两种方式，一是单例，二是对象池，我们使用的数据库连接池、线程池都是对象池化技术，这是典型的用空间换取时间的策略，另一方面也实现对资源的复用，从而避免了不必要的创建和释放资源所带来的开销

### 33. 关于无状态和有状态对象的区别（绝对重点，高逼格，用于Spring MVC 中的controller默认是单例，Spring中的dao，service默认是单例，servlet默认单例，struts2中action多例）

**第一：基本概念：** 

1、有状态就是有数据存储功能。有状态对象(Stateful Bean)，就是有实例变量的对象，可以保存数据，是非线程安全的。在不同方法调用间不保留任何状态。 
2、无状态就是一次操作，不能保存数据。无状态对象(Stateless Bean)，就是没有实例变量的对象.不能保存数据，是不变类，是线程安全的。

**第二：Spring中的有状态(Stateful)和无状态(Stateless)** 

1.通过上面的分析，相信大家已经对有状态和无状态有了一定的理解。无状态的Bean适合用不变模式，技术就是单例模式，这样可以共享实例，提高性能。有状态的Bean，多线程环境下不安全，那么适合用Prototype原型模式。Prototype: 每次对bean的请求都会创建一个新的bean实例。 

2.默认情况下，从Spring bean工厂所取得的实例为singleton（scope属性为singleton）,容器只存在一个共享的bean实例。 

3.理解了两者的关系，那么scope选择的原则就很容易了：有状态的bean都使用prototype作用域，而对无状态的bean则应该使用singleton作用域。 

4.如Service层、Dao层用默认singleton就行，虽然Service类也有dao这样的属性，但dao这些类都是没有状态信息的，也就是相当于不变(immutable)类，所以不影响。Struts2中的Action因为会有User、BizEntity这样的实例对象，是有状态信息的，在多线程环境下是不安全的，所以Struts2默认的实现是Prototype模式。在Spring中，Struts2的Action中，scope要配成prototype作用域。  

**第三：Servlet是单例模式**

Servlet体系结构是建立在Java多线程机制之上的，它的生命周期是由Web 容器负责的。一个Servlet类在Application中只有一个实例存在，也就是有多个线程在使用这个实例。这是单例模式的应用。无状态的单例是线程安全的，但我们如果在Servlet里用了实例变量，那么就变成有状态了，是非线程安全的。

**Out,Request,Response,Session,Config,Page,PageContext是线程安全的,Application在整个系统内被使用,所以不是线程安全的.**

**第四：SpringMvc默认是单例默认，Struts2默认的实现是Prototype模式。**

**==总结：无状态的单例是线程安全的，有状态的对象是非线程安全的==**

### 34.  什么是领域模型(domain model)？贫血模型(anaemic domain model)和充血模型(rich domain model)有什么区别？

答：领域模型是领域内的概念类或现实世界中对象的可视化表示，又称为概念模型或分析对象模型，它专注于分析问题领域本身，发掘重要的业务领域概念，并建立业务领域概念之间的关系。贫血模型是指使用的领域对象中只有setter和getter方法（POJO），所有的业务逻辑都不包含在领域对象中而是放在业务逻辑层。有人将我们这里说的贫血模型进一步划分成失血模型（领域对象完全没有业务逻辑）和贫血模型（领域对象有少量的业务逻辑），我们这里就不对此加以区分了。充血模型将大多数业务逻辑和持久化放在领域对象中，业务逻辑（业务门面）只是完成对业务逻辑的封装、事务和权限等的处理

例如：user的发邮件和校验，是放在user这个实体类还是放在UserService这个服务接口中，如果放在User实体类，看似很合理，user发邮件和校验等等都是user自己息息相关的业务逻辑，可能方便维护；而如果将发邮件和校验等逻辑抽取出来抽取出来放在业务层，个人感觉也是很不错的啊...

### 35. 谈一谈测试驱动开发（TDD）的好处以及你的理解

答：TDD是指在编写真正的功能实现代码之前先写测试代码，然后根据需要重构实现代码

- 更清晰的代码 — 只写需要的代码 
- 更好的设计 
- 更出色的灵活性 — 鼓励程序员面向接口编程 
- 更快速的反馈 — 不会到系统上线时才知道bug的存在

[测试驱动开发之初窥门径](https://blog.csdn.net/jackfrued/article/details/44433249)



## 四. JAVA笔试题

### 1. equals 和 == 区别？

#### 1.1 == 可以比较基本数据类型和引用数据类型，对于基本类型就是比较值，对于引用类型就是比较内存地址

#### 1.2 equals 是Object 的方法，不能比较基本数据类型，只能比较引用数据类型，如果该方法没有被重写过默认也是 ==（比较内存地址）

#### 我==们可以看到String类的equals方法是被重写过的，而且String类在日常开发中用的比较多，久而久之，形成了equals是比较值的错误观点== 



### 2. hashset 和hashmap的区别？

#### 2.1 看源码：hashset其实就是hashmap实现的

```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{
    static final long serialVersionUID = -5024744406713321676L;

    private transient HashMap<E,Object> map;

    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    /**
     * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
     * default initial capacity (16) and load factor (0.75).
     */
    public HashSet() {
        map = new HashMap<>();
    }
```

#### 2.2 hashset的add（e）方法

```java
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

也就是说我们调用hashset的add（e）方法，调用的就是hashmap的put（key，value）方法，传入的参数就是hashmap的key，而value是固定的一个PRESENT值（就是一个Ojbect 对象）

#### 2.3 为什么重写equals方法的同时要重写hashcode方法？

==重写equals方法可以实现我们自定义的逻辑上的相等==

以Person为例：通过重写equals方法，name相等和age相等我们视为同一个人

==而对于hashmap 而言，判断key的不同就是根据hashcode值来进行判断，如果我们不重写equals方法的同时重写hashcode方法，即便我们认为相同的两个对象，hashmap也认为不同==（Object的hashcode方法是native类型，由于Java语言无法访问操作系统底层信息（比如：底层硬件设备等），所以需要借助C语言来完成，简而言就是Java中声明的可调用的使用C/C++实现的方法），也会放进数据结构中；

这里比较有意思的是：通过工具复写的hashcode方法生成的hashcode值和我们的equals方法比较的属性是一致的（如果我们认为name相等的Person就是同一个人，hashcode值就只会根据name来生成）

关于重写hashcode方法中的数字31：减少hash地址冲突，n*31可以被优化为（n<<5）-n，移位和减法操作效率大大高于乘法，而且31只占用5bit

##### - 示例代码

```java
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class Person {

    private String name;
    private Integer age;

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + ((age == null) ? 0 : age.hashCode());
        result = prime * result + ((name == null) ? 0 : name.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Person other = (Person) obj;
        if (age == null) {
            if (other.age != null)
                return false;
        } else if (!age.equals(other.age))
            return false;
        if (name == null) {
            if (other.name != null)
                return false;
        } else if (!name.equals(other.name))
            return false;
        return true;
    }
}
```

##### - 测试代码

```java
@Slf4j
class EqualsTest {

    
    @Test
    void test() {
        
        String string = new String("cris");
        String string2 = new String("cris");
        log.info("string == string2 是：{}", string == string2);              // false
        log.info("string.equals(string2) 是：{}", string.equals(string2));     // true
        System.out.println(1000 == new Integer(1000));   // true（包装类自动转换为基本数据类型）
        System.out.println(new Integer(2000).equals(2000));      // Integer覆写equals，比较值
        
        Integer integer = new Integer(9090);
        Integer integer2 = new Integer(9090);
        System.out.println(integer.equals(integer2));                           // 注意：Integer复写了equals方法，就是比较值
        System.out.println(integer == integer2);                                // false
        
        
        Set<String> set = new HashSet<>();
        set.add(string);
        set.add(string2);
        log.info("set 的size是：{}", set.size());                              // 1
        
        log.info("------------------------------------");
        
        Person p1 = new Person("cris", 12);
        Person p2 = new Person("cris", 12);
        log.info("p1 == p2  是：{}", p1 == p2);                               // false
        log.info("p1.equals(p2)  是：{}", p1.equals(p2));                     // false
        
        Set<Person> set2  = new HashSet<>();
        set2.add(p1);
        set2.add(p2);
        log.info("set2的size是：{}", set2.size());       // 不覆写equals和hashcode就是 2
        
    }
}
```

### 3. String 笔试题

- String 的常量池和new String（）方法（创建一个或者两个对象）

- String的intern（）入池方法及返回值

- String的常量+（找池） 和变量+（堆内存新建对象）,比较坑爹

  ![1527854053634](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527854053634.png)

  ​

  ```java
  @Slf4j
  class StringTest {

      @Test
      void test() {
          
          // 生成两个对象（字符串常量池没有cris的情况下），常量池一个cris，堆内存一个cris，占内存s1 指向堆内存的cris
          String s1 = new String("cris");
          // 返回常量池的cris的内存地址
          String s2 = "cris";
          // 生成一个对象，只有堆内存又生成一个cris，占内存的s13 指向这个新增的cris
          String s3 = new String("cris");
          
          log.info("s1 == s2:{}", s1 == s2);        // false
          log.info("s1 == s3:{}", s1 == s3);        // false
          log.info("s2 == s3:{}", s2 == s3);      // false
          
          // 字符串引用.intern()表示将当前字符串对象入常量池并返回常量池的字符串引用
          // 如果当前常量池已经有相同的字符串了（equals方法比较），那么就返回常量池中这个字符串引用
          log.info("s1 == s1.intern():{}", s1 == s1.intern());        // false
          log.info("s2 == s2.intern():{}", s2 == s2.intern());        // true
          log.info("s1.intern() == s2.intern():{}", s1.intern() == s2.intern());    //true
          
          String string = "java";
          String string2 = "ja";
          String string3 = "va";
          // 常量（+）找池，变量（+）就在堆新建对象；注意String 是final 类哦
          System.out.println(string == string2 + string3);        // false
          System.out.println(string == string2 + "va");           // false
          System.out.println(string == "ja" + "va");              // true
          System.out.println(string == "java");                   // true
          System.out.println(string2 + string3 == string2 + "va");          // false
          
      }
  }
  ```

### 4. java中的多态

- ==方法重载体现的是一个类里面的多态（编译时多态）；方法重写是父类和子类之间的多态性（运行时多态）的一种体现==；

- 事实上，java里面体现多态的是方法的重写而不是方法的重载（Thinking in Java）原话，因为多态需要继承的支持

- **多态是运行时行为**  

  ```java
  interface Animal{
      void eat();
  }
  class Cat implements Animal{

      @Override
      public void eat() {
          System.out.println("cat eat fish");
      }
    
  }
  class Dog implements Animal{

      @Override
      public void eat() {
          System.out.println("dog eat bone");
      }
      
  }
  class AnimalFactory {
      public static Animal getInstance(Integer integer) {
          Animal animal = null;
          
          switch (integer) {
          case 0:
              animal = new Cat();
              break;

          default:
              animal = new Dog();
              break;
          }
          return animal;
      }
  }

  class PolymorphismTest {

      @Test
      void test() {
          Animal instance = AnimalFactory.getInstance(new Random().nextInt(2));         // 0,1
          instance.eat();
      }
  }
  ```

### 5. 值传递和引用传递

![1527854309674](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527854309674.png)

```java
@Data
class Person{
    private String name;
}
class CiteTest {

    public static void changeString(String string) {
        string = "123";
    }
    
    public static void changeName(Person person) {
        person.setName("cris");
    }
    
    public static int changeInt(int age) {
        age = 40;
        return age;
    }
    
    @Test
    void test() {
        
        int i = 10;
        changeInt(i);
        System.out.println(i);      // 10   基本数据类型传递的是值的副本
        
        
       Person person =  new Person();
       person.setName("zc");
       changeName(person);
       System.out.println(person.getName());    // cris   引用传递新生成的引用改变堆内存中对象的属性（根据Thinking in Java，其实还是值传递）
       
       String string = "abc";
       changeString(string);
       System.out.println(string);             // abc     引用传递新生成的引用改变原本的指向
        
}
```

### 6. 代码执行顺序

![1527856070570](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527856070570.png)

终结：父静态代码块--》子静态代码块--》父代码块--》父构造方法--》子代码块--》子构造方法

### 7. Serializable 详解

1. ==空接口，用于序列化，实现了这个接口的对象可以转换为一系列字节并且之后恢复回来==
2. ==网络过程中用于传输数据，即序列化机制可以自动补偿操作系统之间的差异==
3. ==Serializable 还可以用于统一参数类型：==
   - findUserById（String id）
   - findCustomerById（Integer id）
   - findById（Serializable id）<BaseDao 中统一上面两种方法，常用于分布式系统>

### 8. 快速去重（利用set的特性）

![1527858939967](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527858939967.png)

```java
public class DuplicateTest {

    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(1);
        list.add(1);
        list.add(2);
        list.add(2);
        list.add(3);
        list.add(3);
        list.add(4);
        list.add(4);
        System.out.println(list.size());            // 9
        
        Set set = new HashSet<>();
        set.addAll(list);													// 利用set的特性快速去重
        
        for (Object object : set) {
            System.out.println(object);             // 1,2,3,4
        }
        set.forEach(System.out::print);             // 1,2,3,4   java 8 语法
        
        list = new ArrayList<>(set);    
        System.out.println(list.size());            // 4
        
    }
}
```



### 9. HashMap（绝对重点，高逼格）以java 7 为例

1.8已经不一样了，底层是数组+链表+红黑树；

并且初始化的时候entry数组长度默认为0；

为什么加入红黑树？就是为了提高比较效率，如果新加入的元素的key的hashcode计算出来的索引正好有元素，如果单纯用链表比较，需要将新加入元素的key和当前索引的所有元素的key比较（equals方法），效率太低；加入红黑树可以有效规避不需要比较的元素，大大提高了效率

七上八下：java7中新添加的元素是放入到当前索引链表的头部（经过全部比较后）；java8中新添加的元素是放入到当前索引链表的底部（选择性的经过比较）

#### 一、hashmap的数据结构？

数组+链表 的数据结构（java7）

##### 详解：当我们调用空的hashMap构造方法时，其实是构造了一个初始值大小默认为16，负载因子为0.75的空HashMap，而负载因子*默认容量 = 扩容临界值（吞吐量）；换句话说，未指定初始值的hashmap容器在容量达到12的时候就开始扩容

优化方案：当我们从数据库查出来的数据量不大且范围固定的时候，直接new HashMap(int initialCapacity, float loadFactor)来装载我们的数据（改变默认扩容机制，提高效率）

![1527917036028](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527917036028.png)



**hashmap中的数组到底什么类型？Entry<K,V> [] 类型的数组**

**而且这个数组是在我们第一次使用put方法的时候才初始化的（默认容量16，负载因子0.75）**

![1527917546799](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527917546799.png)

#### 二、hashmap的put方法详解

**hashmap可以传递null作为key！**

![1527918034203](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527918034203.png)

put方法实现步骤：

根据key的hashcode值计算出它在数组中的哪一个位置 （默认数组大小范围0-15）；然后进行添加流程

1. 如果当前位置没有对象，那么创建一个entry对象并放入数组

![1527918670326](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527918670326.png)

![1527919058203](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527919058203.png)

- 放入数组时的扩容判断

![1527919592833](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527919592833.png)



2. 如果当前位置已经有对象了

   ![1527920391111](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527920391111.png)

   - 如果当前新key和老key不是同一个key或者equals不相等，那么就在当前位置形成entry的链表结构；新添加的entry对象位于entry链的头部

#### 三、hashmap的get方法详解

![1527922325916](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527922325916.png)

![1527922537582](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527922537582.png)

- 总结：hashmap的get方法实际上就是根据key的hashcode值经过hash算法找到entry数组上的entry链表，遍历链表找到key为同一个引用或者equals相等的entry对象，拿到这个entry对象的value值；就是hashmap的get方法返回的value值

  ![1527922735065](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527922735065.png)

- **key值为null，默认hashmap将其创建的entry对象放到数组的第一个位置**

- ​

#### 四、测试代码

```java
public class HashMapTest {

    @Test
    public void test1() {
        Map<String, Integer> map = new HashMap<>();
        map.put("1", 123);
        map.put(null, 3);
        System.out.println(map.get(null));              // 3
        
        System.out.println(false && true || true);      // true    java 中 && 优先级高于 ||
        System.out.println(true || true && false);      // true
    }
    
}
```

#### 五、LinkedHashMap

相比与HashMap，在Entry元素中使用了指向前一个添加的Entry元素的Entry类型属性before和指向后一个添加的Entry元素的Entry类型属性after；其实就是为Entry元素添加了链表的指针（向前和向后两个指针）来记录元素添加的顺序，[详情参考](https://www.cnblogs.com/xrq730/p/5052323.html)

### 10. List详解（绝对重点）

#### - Arraylist

当我们new 一个ArrayList的时候，其内部就是生成了一个初始容量为10的Object类型的动态数组（java7）

当我们new 一个ArrayList的时候，其内部就是生成了一个初始容量为0的Object类型的动态数组（java8）；只有在第一次添加元素的时候才初始化数组长度为10

细节优化：如果我们对从数据库查询出来的数据量有一个大致的估算，尽量为新建的ArrayList对象赋予一个初始容量值，尽可能的减少Arraylist里面的数组扩容次数

![1527944088605](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527944088605.png)



LinkedList是双向循环链表结构

![1527941228625](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527941228625.png)

当我们new 一个LinkedList的时候，构造一个空列表；通过静态内部类Node节点来维护数据结构

![1527941574007](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527941574007.png)

#### - java 7 中的linkedList数据结构的add方法（手写出来，逼格）

![1527943148959](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527943148959.png)

![1527943566154](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527943566154.png)

#### - ArrayList和Vector的区别：

Vector属于古老的同步类，开销大，访问也慢，虽然是线程安全的，但是很少用，并且每次扩容2倍；而ArrayList是1.5倍

### 11. http和https 协议（绝对重点）

#### - https协议：由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议更加安全

#### - HTTPS和HTTP的区别主要如下：

　　1、https协议需要申请证书，一般免费证书较少，因而需要一定费用。

　　2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议，更安全。

　　3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

#### - HTTPS存在不足之处的：

　　（1）HTTPS协议握手阶段比较费时，会使页面的加载时间延长近50%，增加10%到20%的耗电；

　　（2）HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗，甚至已有的安全措施也会因此而受到影响；

　　（3）SSL证书需要钱，功能越强大的证书费用越高，个人网站、小网站没有必要一般不会用

#### - HTTPS 全过程图示

![1527946748632](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527946748632.png)

#### - HTTP中的三次握手协议（基于TCP协议）

在TCP/IP协议中，TCP协议提供可靠的连接服务，采用三次握手建立一个连接。 

- 第一次握手：建立连接时，客户端发送数据包到服务器，并进入等待服务器确认状态； 
- 第二次握手：服务器收到数据包，同时自己也发送一个数据包，此时服务器进入等待连接状态； 
- 第三次握手：客户端收到服务器的数据包，向服务器发送确认包，此包发送完毕，客户端和服务器进入建立连接状态，完成三次握手。 完成三次握手，客户端与服务器开始传送数据.

#### - HTTP中的四次挥手协议（基于TCP协议的双通道数据传输通道）

TCP/IP是全双工的，每个方向都必须单独进行关闭。有一方完成它的数据发送任务后就能发送一个FIN来终止这个方向的连接。
比如客户端给服务端发送一个FIN，告诉服务端，我再也没有数据要传给你啦，这是第一次挥手；
服务端收到后返回一个ACK告诉客户端，好的，知道啦，这是第二次挥手。客户端收到确认后就可以关闭往服务端那边的数据传输通道了，这个时候服务端仍然可以往客户端继续发送数据。
待服务端也再没有数据要往客户端发送时，就也发一个FIN到客户端，告诉它，我也没啥要传给你了，这是第三次挥手。
客户端得知后在返回一个ACK告诉服务端，好的，收完了，服务端也就可以安心关闭往客户端的数据传输通道了，这是第四次挥手。自此双向的传输通道都已关闭，连接成功释放

#### - 关于HTTP和TCP/IP

【HTTP与TCP/IP】
我们知道网络由下往上分为7层：物理层、数据链路层、网络层、传输层、会话层、表示层和应用层。
TPC/IP协议是传输层协议，主要解决数据如何在网络中传输。
HTTP是应用层协议，主要解决如何包装数据。
所以说HTTP是基于TPC/IP的，与HTTP类似也是基于TPC/IP的还有FTP啊这类的应用层协议

### 12. 常见的web状态码

1. 206：断点下载，表示服务器已经成功处理了部分 GET 请求
2. 301：永久跳转，原地址不存在，url指向另外一个地址
3. 400：请求参数非法，表单提交数据到业务层
4. 413：上传文件大于服务器限制
5. 500：服务器内部错误
6. 503：服务器停止了服务

### 13. 多线程（极度重点）

进程和线程：

- 进程是资源的分配和调度的一个独立单元，而线程是CPU调度的基本单元
- 同一个进程中可以包括多个线程，并且线程共享整个进程的资源（寄存器、堆栈、上下文），一个进行至少包括一个线程
- 线程中执行时一般都要进行同步和互斥，因为他们共享同一进程的所有资源
- 线程是轻量级的进程，它的创建和销毁所需要的时间比进程小很多，操作系统中的执行功能都是创建线程去完成的

#### - Lock 锁及案例

如果使用synchronized关键字，永远只能有一个线程获取锁后访问或者操作共享资源

Lock锁提供了更加广泛和细粒度的控制

最规范案例

```java
@Slf4j
@Data
class Ticket {

    private Integer tickets = 50;
    Lock lock = new ReentrantLock();

    public void sale() {

        while (this.tickets > 0) {
            try {
                lock.lock();
                Thread.sleep(500);
                log.info(Thread.currentThread().getName() + "正在买第：{}张票，还剩下{}张票", 50 - (--tickets), tickets);

            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
    }

}

// 再次强调，多线程测试不要放在@Test方法中进行测试，再三强调！！！
@Slf4j
public class ThreadTest {

    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(() -> { // java中的lambda表达式完美解决匿名内部类的书写（编程式接口类型）
            ticket.sale();
        }, "aa").start();

        new Thread(() -> {
            ticket.sale();
        }, "bb").start();

        new Thread(() -> {
            ticket.sale();
        }, "cc").start();
    }
}
```

#### - 通过多线程并发计算得到返回值的callable接口（将任务肢解为多块计算最后结果（FutureTask）汇总）

```java
// 创建执行线程的第三种方式：实现Callable 接口，相较于实现Runnable 接口的方式，方法可以具有返回值，并且还要抛出异常
// 执行Callable 的call 方法，还需要FutureTask 类的支持，用于接收运算的结果，futureTask是Future 和Runnable 接口的实现类
public class TestCallable {

    public static void main(String[] args) {
        
        CallableDemo demo = new CallableDemo();
        FutureTask<Integer> task = new FutureTask<>(demo);
        
       // 开启多线程执行同一个任务（也可以使用线程池）
        for (int i = 0; i < 3; i++) {
            new Thread(task).start();
        }
        
        try {
            System.out.println("-------------");            // main线程执行到这里打住了
            System.out.println("结果是：" + task.get());        // FutureTask 可实现类似闭锁的效果；
        } catch (InterruptedException | ExecutionException e) {
            
            e.printStackTrace();
        }   
    }
}

class CallableDemo implements Callable<Integer>{

    Integer sum = 0;
    @Override
    public Integer call() throws Exception {
        for (int i = 0; i <= 100; i++) {
            sum += i;
            System.out.println(Thread.currentThread().getName()+ "------" + sum);
        }
        return sum;
    }   
}
```

####  - 多线程之间的通信（wait和notify方法都是Object的方法哦）

##### 1. 笔试题：请写出Object的五个方法

1. wait
2. notify
3. notifyAll
4. equals
5. clone
6. hashcode
7. toString

##### 2. 只有两个线程分别对共享资源进行不同的操作，可以使用线程通信保证操作的顺序，如果有多个线程对同一个操作都执行，那么就会出现虚假唤醒了！

多线程的条件判断不允许使用if，只能使用while！（这么理解：lock保证线程操作的互斥性，while保证多线程操作同一任务的虚假唤醒问题，condition保证线程之间的通信唤醒和排序问题）

##### 3. 模板案例

```java
@Slf4j
class ShareNumber {

    private Integer num = 0;

    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void increment() {
        lock.lock();
        try {
            // 不满足条件就睡眠（while循环有效避免虚假唤醒）
            while (num != 0) {
                condition.await();
            }
            // 满足条件就执行，并唤醒
            log.info(Thread.currentThread().getName() + "执行了加操作，num={}", ++num);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() {
        lock.lock();
        try {
            while (num == 0) {
                condition.await();
            }
            log.info(Thread.currentThread().getName() + "执行了减法操作，num={}", --num);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

}

public class ThreadCommunicateTest {

    public static void main(String[] args) {
        ShareNumber shareNumber = new ShareNumber();

        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                shareNumber.increment();
            }

        }, "aa").start();
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                shareNumber.decrement();
            }
        }, "bb").start();
        
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                shareNumber.increment();
            }
        }, "cc").start();
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                shareNumber.decrement();
            }
        }, "dd").start();

    }
}
```

##### 4. 使用lock和condition的线程接力经典例题（绝对高薪）

```java
// 编写一个程序，开启三个线程 A,B,C,
// A线程将自己id打印5次，然后B线程将自己id打印10次，C线程将自己id打印15次，此为一轮，一共执行10轮，手写代码
// 一个主类负责线程之间的通信和上锁，开锁，三个实现了Runnable接口的线程类执行控制类的方法即可，主要逻辑实现都在主类里面
public class TestThreadWithOrder {

    public static void main(String[] args) {
        
        ThreadOrder order = new ThreadOrder();
        
        // 这里使用java8的 lambda表达式来完成实现Runnable 接口的线程类
        new Thread(() -> {
            
            for (int i = 1; i <= 10; i++) {
                order.loopA(i);
            }
            }, "线程A").start();
        
        new Thread(() -> {
            
            for (int i = 1; i <= 10; i++) {
                order.loopB(i);
            }
        }, "线程B").start();
        
        new Thread(() -> {
            
            for (int i = 1; i <= 10; i++) {
                order.loopC(i);
                System.out.println("---------------------------------------------------");
            }
        }, "线程C").start();

    }

}

// 专门写一个类用于控制线程之间的顺序。依次按照规定等待，执行对应业务逻辑然后顺序唤醒其他线程
// 通过lock 锁实现线程执行任务的互斥性；通过condition 完成线程之间的按序通信（重点）
class ThreadOrder{

    private Lock lock = new ReentrantLock();

    private int num = 1;
    
    private Condition con1 = lock.newCondition();
    private Condition con2 = lock.newCondition();
    private Condition con3 = lock.newCondition();

    public void loopA(int loop) {

        lock.lock();

        try {
            
            if(num != 1) {
                con1.await();
            }
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + "----->第" + loop + "轮");
            }
            
            num = 2;
            con2.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }

    }
    public void loopB(int loop) {

        lock.lock();

        try {
            
            if(num != 2) {
                con2.await();
            }
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + "----->第" + loop + "轮");
            }
            
            num = 3;
            con3.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }

    }
    public void loopC(int loop) {

        lock.lock();

        try {
            
            if(num != 3) {
                con3.await();
            }
            for (int i = 0; i < 15; i++) {
                System.out.println(Thread.currentThread().getName() + "----->第" + loop + "轮");
            }
            
            num = 1;
            con1.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```



##### 5. 线程八锁

![1527997219074](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1527997219074.png)

```java
/*
 * 1. 一个对象，两个普通同步方法，两个线程分别执行，打印？1 2
 * 2. 一个对象，getOne方法新增Thread.sleep（1000）方法，打印？1 2
 * 3. 一个对象，新增一个普通方法getThree（），打印？3 1 2
 * 4. 两个对象，两个普通同步方法，打印? 2 1
 * 5. 一个对象，getOne方法为静态同步方法，打印？2 1
 * 6. 一个对象，两个方法均为静态同步方法，打印？1 2
 * 7. 两个对象，一个为静态同步方法，一个为普通同步方法，打印？2 1
 * 8. 两个对象，两个方法均为静态同步方法，打印？1 2
 * 
 * 线程八锁：
 * ① 非静态同步方法的锁为this，静态同步方法的锁为对应的Class对象
 */
```

##### 6. 读写锁

```java
// 读写锁：针对多个线程对于共享数据的读和写做出优化
// 写写/读写 需要采用写锁
// 读读 可以使用读锁来提高多线程效率
public class TestReadWriteLock {

    public static void main(String[] args) throws InterruptedException {
        ReadWriteDemo demo = new ReadWriteDemo();

        new Thread(() -> {
            demo.write((int) (Math.random() * 101));
        }, "写线程").start();
        
        // 为了防止读线程读到写线程还未写入的数据，可以进行线程休眠，也可以在读线程进行非空判断（效率低，更精准）
        Thread.sleep(100);

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {

                    e.printStackTrace();
                }
                demo.read();
            }, "读线程" + i).start();
        }

    }

}

class ReadWriteDemo {

    private int i = 0;
    // 读写锁
    private ReadWriteLock lock = new ReentrantReadWriteLock();

    public void read() {
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读数据：" + i);
        } finally {
            lock.readLock().unlock();
        }
    }

    public void write(int i) {
        lock.writeLock().lock();
        try {
            this.i = i;
            System.out.println(Thread.currentThread().getName() + "写数据为：" + this.i);
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

##### 7. 死锁

常用于synchronized关键字，lock锁不正确释放也会导致，模板就是两个方法，分别执行两个synchronized代码块，分别需要两个锁（通常两个静态变量），然后各自拿着自己第一次执行的锁，不给对方又想要执行对方的锁的代码块

```java
public class SyncDeadLock{  
    private static Object locka = new Object();  
    private static Object lockb = new Object();  
  
    public static void main(String[] args){  
        new SyncDeadLock().deadLock();  
    }  
  
    private void deadLock(){  
        Thread thread1 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                synchronized (locka){  
                    try{  
                        System.out.println(Thread.currentThread().getName()+" get locka ing!");  
                        Thread.sleep(500);  
                        System.out.println(Thread.currentThread().getName()+" after sleep 500ms!");  
                    }catch(Exception e){  
                        e.printStackTrace();  
                    }  
                    System.out.println(Thread.currentThread().getName()+" need lockb!Just waiting!");  
                    synchronized (lockb){  
                        System.out.println(Thread.currentThread().getName()+" get lockb ing!");  
                    }  
                }  
            }  
        },"thread1");  
  
        Thread thread2 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                synchronized (lockb){  
                    try{  
                        System.out.println(Thread.currentThread().getName()+" get lockb ing!");  
                        Thread.sleep(500);  
                        System.out.println(Thread.currentThread().getName()+" after sleep 500ms!");  
                    }catch(Exception e){  
                        e.printStackTrace();  
                    }  
                    System.out.println(Thread.currentThread().getName()+" need locka! Just waiting!");  
                    synchronized (locka){  
                        System.out.println(Thread.currentThread().getName()+" get locka ing!");  
                    }  
                }  
            }  
        },"thread2");  
  
        thread1.start();  
        thread2.start();  
    }  
}  
```

##### 8. 线程池

**常用的三大S工具类：Arrays，Collections，Executors**

==针对多个独立的业务，可以使用线程池开启多个线程来分别处理这些请求（注意：这和多线程执行一个耗时任务不同，当然也可以使用线程池执行同一个任务）==

- 常用的几种线程池示例代码

  ```java
  /*
   * 一、线程池：提供了一个线程队列，队列中保存着所有等待状态的线程。避免了创建与销毁额外开销，提高了响应的速度。
   * 
   * 二、线程池的体系结构：
   *  java.util.concurrent.Executor : 负责线程的使用与调度的根接口
   *      |--**ExecutorService 子接口: 线程池的主要接口
   *          |--ThreadPoolExecutor 线程池的实现类
   *          |--ScheduledExecutorService 子接口：负责线程的调度
   *              |--ScheduledThreadPoolExecutor ：继承 ThreadPoolExecutor， 实现 ScheduledExecutorService
   * 
   * 三、工具类 : Executors 
   * ExecutorService newFixedThreadPool(int capacity) : 创建固定大小的线程池
   * ExecutorService newCachedThreadPool() : 缓存线程池，线程池的数量不固定，可以根据需求自动的更改数量。
   * ExecutorService newSingleThreadExecutor() : 创建单个线程池。线程池中只有一个线程
   * ScheduledExecutorService newScheduledThreadPool() : 创建固定大小的线程，可以延迟或定时的执行任务。
   */
  public class TestThreadPool {

      public static void main(String[] args) throws InterruptedException, ExecutionException {

          // 初始化变量一定要设置默认值，即便是null
          ExecutorService pool = null;
          List<Future<Integer>> list = null;

          try {
              // 1. 创建线程池
              pool = Executors.newFixedThreadPool(5);

              ThreadPoolDemo demo = new ThreadPoolDemo();
              list = new ArrayList<>();

              /*
               * for (int i = 0; i < 10; i++) { 
               *  Future<Integer> task = pool.submit(()->{ 
               *  
               *      int sum = 0; 
               *      for (int j = 0; j <= 100; j++) { 
               *          sum += j; 
               *         }; 
               *          return sum; 
               *      
               *     });
               *      // 将每个线程的计算结果收集起来
               *      list.add(task);
               * }
               */
              // 2. 为线程池中的线程分配任务
              for (int i = 0; i < 10; i++) {
                  pool.submit(demo);
              }
          } catch (Exception e) {
              e.printStackTrace();
          } finally {

              // 关闭线程池
              pool.shutdown();
          }

         /* for (Future<Integer> future : list) {
              System.out.println(future.get());
          }*/

      }
  }

  class ThreadPoolDemo implements Runnable {

      private int sum = 0;

      @Override
      public void run() {
          for (int i = 0; i <= 100; i++) {
              sum += i;
              System.out.println(Thread.currentThread().getName() + "---->" + sum);
          }
      }

  }
  ```

- 定时任务线程池

  ```java
  public class TestScheduleThreadPool {

      public static void main(String[] args) throws InterruptedException, ExecutionException {
          
          ScheduledExecutorService pool = Executors.newScheduledThreadPool(5);
          
          try {
              // 可以定时以及延迟的执行任务的线程池
              for (int i = 0; i < 5; i++) {
                  ScheduledFuture<Integer> task = pool.schedule(()->{
                      int sum = new Random().nextInt(100);
                      return sum;
                  }, 1, TimeUnit.SECONDS);
                  
                  System.out.println(task.get());
              }
          } catch (Exception e) {
              
              e.printStackTrace();
          }finally {
              
              // 记住关掉线程池
              pool.shutdown();
          }
      }
  }
  ```

### 14. Git冲突解决（暂略）

### 15. Mysql 优化策略（极度重点暂略）

#### - 索引（重点）	

### 16. Linux 常见问题及命令（重点）

#### - 整机性能查看（top和uptime）

![1528017662869](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528017662869.png)

[top参数详解](https://www.cnblogs.com/kevingrace/p/6668149.html)

#### - cpu性能查看（vmstat -n 2 3）

![1528019325484](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528019325484.png)

根据经验：us+sy 》80% 表示cpu负担较大	

#### - 内存性能评估（free -m)

![1528020415633](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528020415633.png)

#### - IO性能评估（iostat -d 2 3）

![1528021014768](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528021014768.png)

#### - 硬盘容量查看（df -h）

![1528021270499](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528021270499.png)

#### - linux 常用的五个命令

1. top+uptime
2. vmstat
3. free -m
4. iostat
5. df -h
6. chmod 777（第一个7表示文件拥有者权限为读写执行，第二个7表示同组用户权限为读写执行，第三个7表示其他组用户权限为读写执行）/ 权限数字：4（r），2（w）,1（x）


### 17. 高CPU负载解决方案（linux上的jvm调优）

##### - top命令查看cpu状态

![1528031615458](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528031615458.png)

##### - PS命令根据PID找到对应的线程TID

![1528031716280](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528031716280.png)

##### - 将占用cpu高的线程ID根据十进制--》十六进制换算（计算器）

![1528032196514](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528032196514.png)

##### - 排查步骤总结（重点就是jstack命令查看线程栈状态）

![1528032335631](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528032335631.png)



## 五、JVM虚拟机快速入门

### 1. JVM结构图

![1528033810022](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528033810022.png)

**JVM依赖于操作系统之上，为我们的java程序提供运行平台**



![1528040476422](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528040476422.png)

![1528040494732](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528040494732.png)

![1528040513776](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528040513776.png)

![1528040521742](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528040521742.png)



### 2. 类加载器（三个）

![1528034713902](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528034713902.png)

#### - JVM的双亲委派机制（采用了沙箱隔离）：

某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父类加载器，依次递归，如果父类加载器可以完成类加载任务，就成功返回；只有父类加载器无法完成此加载任务时，才自己去加载；

这么做的目的就是为了防止java源代码被篡改，也保证了我们java程序的安全性

#### - JVM的堆的结构图

![1528035871856](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528035871856.png)

注意：当我们new book（）的时候，对象实际上存在于堆内存的伊甸区，而永久代（java7及之前）和元空间（java8）实际上是非堆内存--》都是对JVM规范中方法区的实现

### 3. JVM常见异常（StackOverFlowError 和 OOM<OutOfMemoryError>）

![1528038515942](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528038515942.png)

StackOverFlowError 常见于方法之间的递归调用，栈内存溢出

最典型的场景就是，在 jsp 页面比较多的情况，容易出现堆内存溢出，即OutOfMemoryError

### 4. JVM调优

#### - java 7 的堆内存结构图

![1528038766893](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528038766893.png)

#### - java 8 的堆内存结构图

![1528038791406](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528038791406.png)

[jvm结构详解](https://www.cnblogs.com/paddix/p/5309550.html)

#### - MAT（Eclipse Memory Analyzer）堆内存分析插件，生成dump文件，快速定位内存泄露

![1528041160473](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528041160473.png)



#### - JAVA GC

![1528041346869](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528041346869.png)



## 六、JAVA 8详解

### 1. 抽象类和接口的异同

#### 1.1 声明方式

抽象类毕竟是个类，需要使用abstract修饰，但不是必须要有抽象方法；

接口用interface修饰，没有构造器

#### 1.2 内部结构

java7的接口只有常量和抽象方法；java8的接口有默认方法（默认所有子类可以使用，不破坏原有的子类实现结构），静态方法；java9的接口可以使用private修饰方法

#### 1.3 共同点

都不能实例化；多态方式的使用

#### 1.4 不同点

单继承vs多实现



## 七、Mysql优化

### 1. 查询引擎比较



![1528126733721](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528126733721.png)

- 所以MyISAM适合查询（效率快）；InnoDB支持行锁，适合高并发操作，以事务为关注点

### 2. 索引（绝对重点）

![1528127300272](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528127300272.png)

索引是帮助mysql高效获取数据的数据结构：排好序的快速查找数据结构（多路搜索树）

![1528133949774](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528133949774.png)

**数据稳定的时候重建索引，可以保持查询的效率不被删除，增加操作破坏**

#### - 索引优势

![1528134348625](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528134348625.png)

#### - 索引劣势

![1528254262749](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528254262749.png)



#### - 索引分类

1. 单值索引:一个索引只包含一个列，一个表可以有多个单值索引

2. 唯一索引：索引列的值必须是唯一的，但允许有空值

3. 复合索引：一个索引包含多个列

4. 基本语法

   ![1528255504707](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528255504707.png)

   ![1528255540630](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528255540630.png)

#### -  索引原理

![1528256242765](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528256242765.png)

#### - 建立索引情况分析

![1528256485260](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528256485260.png)

![1528256679450](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528256679450.png)







### 3. sql语句

user表和score表中按照userId关联查询，根据姓名分组查询出数学分数大于80分并按照分数降序排序的女生姓名，分数，班级名，共计前十条数据

```sql
select u.name as uName,s.math as sMath,u.classId as uClassId from tb_user u inner join tb_score s on s.useId = u.userId where u.sex = 0 GROUP BY uName HAVING sMath > 80 order by Smath DESC limit 10;
```

- 注意需要设置

  ```sql
  select @@global.sql_mode;
  select @@session.sql_mode;
  set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
  set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
  ```

- 注意：mysql 引擎从from后面的语句开启解析

  ![1528132327346](C:\File\Typora\JAVA 面试题总结\JAVA 面试题总结.assets\1528132327346.png)

### 4. 七种join 连接

