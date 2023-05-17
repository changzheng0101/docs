# Optional

>在java8引入的，可以将这个类理解为一个盒子，任何数据都可以放进去，这个类存在的意义是**告诉调用者这个可能为NULL**。

## 传统写法

传统代码中会有很多校验NULL的代码为了不引发空指针异常，很多时候程序员还是会忘记校验空指针。

```java
public class OptionalTest {
    public static void main(String[] args) {
        Cat cat = findCatByName("catName");
        if (cat == null) {
            System.out.println("no cat found");
        } else {
            System.out.println(cat);
        }
    }

    public static Cat findCatByName(String name) {
        return null;
    }
}
```

## Optional

```java
public class OptionalTest {
    public static void main(String[] args) {
        Optional<Cat> catOptional = findCatByName("catName");
    }

    public static Optional<Cat> findCatByName(String name) {
        Cat cat = new Cat(name);
        return Optional.ofNullable(cat);
    }
}
```

`Optional.ofNullable(value)`

* value可以为null
* 其实是两个函数的结合版本，底层是调用以下两个
  * `Optional.empty()`将会返回一个包含NULL的Optional
  * `Optional.of(value)`不接受NULL

### 一种错误用法

````java
Optional<Cat> catOptional = findCatByName("catName");
if (catOptional.isPresent()) {
    System.out.println(catOptional.get());
} else {
    System.out.println("No cat found");
}
````

这种用法和上面的判断null非常类似，可以说Optional几乎没有发挥作用。get方法将会返回Optional中的内容，当内容不存在的时候会抛出异常。

>get方法其实和orElseThrow方法等价，但是通常推荐使用后者

### 改进

较为好的解决办法是提供默认值，以下方面提供了一个默认的cat

```java
Optional<Cat> catOptional = findCatByName("catName");
Cat cat = catOptional.orElse(new Cat("default name"));
```

## 使用map

```java
Optional<String> name = catOptional.map(Cat::getName);
```

可以在Optional上使用map，map之后仍然是一个Optional的对象，所以不用担心为空的状态。