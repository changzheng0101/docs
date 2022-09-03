# lambda

通过lambda表达式，可以大大简化代码，对于只用一次的那种类，可以直接用lambda表达式。

>**接口内必须只有一个方法**

## 原始例子

类实现接口之后，在main函数中声明之后调用方法。

```java
public class LambdaTest {
    public static void main(String[] args) {
        say test = new person();
        test.sayHello();
    }
}

interface say{
    void sayHello();
}

class person implements say{
    @Override
    public void sayHello() {
        System.out.println("outside");
    }
}
```

## 内部静态类

```java
public class LambdaTest {
    static class person implements say {
        @Override
        public void sayHello() {
            System.out.println("outside");
        }
    }

    public static void main(String[] args) {
        say test = new person();
        test.sayHello();
    }
}

interface say {
    void sayHello();
}
```

## 内部类

```java
public class LambdaTest {
    public static void main(String[] args) {
        class person implements say {
            @Override
            public void sayHello() {
                System.out.println("outside");
            }
        }
        say test = new person();
        test.sayHello();
    }
}
```

## 匿名内部类

```java
public class LambdaTest {
    public static void main(String[] args) {
        say test = new say() {
            @Override
            public void sayHello() {
                System.out.println("outside");
            }
        };
        test.sayHello();
    }
}
```

## lambda表达式

```java
public class LambdaTest {
    public static void main(String[] args) {
        say test = ()-> System.out.println("outside");
        test.sayHello();
    }
}
```

有参数的情况,只有一个参数可以省略括号，在接口中已经声明了类型，不用再次声明类型。

```java
public class LambdaTest {
    public static void main(String[] args) {
        say test = (a,b)-> System.out.println(a+b);
        test.sayHello();
    }
}
```

