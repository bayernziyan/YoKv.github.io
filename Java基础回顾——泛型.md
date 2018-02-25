---
title: 泛型
date: 2017-08-22 15:30:16
categories: 
- Java基础
---

## 泛型

<!--more-->

可以用Object来代替，并且在Java SE5之前，我们也只能这么做，由于Object是所有类型的基类，所以可以直接转型。但是这样灵活性还是不够，因为还是指定类型了，只不过这次指定的类型层级更高而已，有没有可能不指定类型？有没有可能在运行时才知道具体的类型是什么？所以，就出现了泛型。

无论何时，只要你能做到，你就应该尽量使用泛型方法。也就是说，如果使用泛型方法可以取代将整个类泛化，那么应该有限采用泛型方法。
```java	

public class Main {

    public static <T> void out(T... args) {
        for (T t : args) {
            System.out.println(t);
        }
    }

    public static void main(String[] args) {
        out("findingsea", 123, 11.11, true);
    }
}
```
类型通配符一般是使用？代替具体的类型实参，注意了，此处’？’是类型实参，而不是类型形参 。再直白点的意思就是，此处的？和Number、String、Integer一样都是一种实际的类型，可以把？看成所有类型的父类。是一种真实的类型。

[来源](http://blog.csdn.net/s10461/article/details/53941091)


### 泛型类与泛型方法

[相关代码](https://coding.net/u/YoKv/p/source-code-test/git/tree/master/src/main/java/space/aiyo/review)

```java

package space.aiyo.review.generics;

public class Foo<A, B> {
    private A a;
    private B b;

    public Foo(A a, B b) {
        this.a = a;
        this.b = b;
    }

    public A method(A a) {
        return a;
    }

    public A method(Foo<A, ?> foo) {
        System.out.println(foo.getB());
        return foo.getA();
    }

    public <K,V> A method(K k,V v) {
        Foo<K,V> foo= new Foo<>(k,v);
        System.out.println(foo.getB());
        return (A)foo.getA();
    }


    public A getA() {
        return a;
    }

    public void setA(A a) {
        this.a = a;
    }

    public B getB() {
        return b;
    }

    public void setB(B b) {
        this.b = b;
    }

    @Override
    public String toString() {
        return "Foo{" +
                "a=" + a +
                ", b=" + b +
                '}';
    }
}

```
### 泛型接口
```java
package space.aiyo.review.generics;

public interface IFoo<A, B, C> {
    A doSomething(B b);

    void exit();

}
```

```java
package space.aiyo.review.generics;

public class ImplFoo implements IFoo<String, Integer, Object> {
    @Override
    public String doSomething(Integer i) {
        return i.toString();
    }

    @Override
    public void exit() {
        System.out.println("exit");
    }
}


```

```java
package space.aiyo.sourcecodetest.generics;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import space.aiyo.review.generics.Foo;
import space.aiyo.review.generics.IFoo;
import space.aiyo.review.generics.ImplFoo;

import java.time.LocalTime;

public class TestG {

    @Before
    public void before() {
        System.out.println(LocalTime.now());
    }

    @After
    public void after() {
        System.out.println(LocalTime.now());
    }

    @Test
    public void classTest() {
        Foo<Integer, String> foo1 = new Foo<>(1, "2");
        System.out.println(foo1.toString());
        Foo<Float, Long> foo2 = new Foo<>(0.13f, 34L);
        System.out.println(foo2.toString());
    }

    @Test
    public void interfaceTest() {
        IFoo<String, Integer, Object> foo = new ImplFoo();
        System.out.println(foo.doSomething(1));
        foo.exit();
    }

    @Test
    public void menthodTest(){
        Foo<Boolean,Integer> foo = new Foo<>(true,2);
        System.out.println(foo.method(false));
        System.out.println(foo.method(false,3));
        System.out.println(foo.method(foo));
    }

}


```