# Proxy

Es6中的一门新技术，代理：代理某个对象，覆盖其中的一些默认行为。

## 覆盖get和set方法

为person对象创建代理，在访问或者设置对象时候，会交由代理执行。

```javascript
const person = {
    name: "test",
    age: 18,
    birth: "2001-01-01"
}

const personProxy = new Proxy(person, {
    set: (target, param, newValue) => {
        console.log("set的代理执行");
        console.log("target-->" + target);
        console.log("param-->" + param);
        console.log("newValue-->" + newValue);
        console.log("-------------------------------");
    },
    get: (target, param) => {
        console.log("get的代理执行");
        console.log("target-->" + target);
        console.log("param-->" + param);
        console.log("-------------------------------");
    }
})

personProxy.age = 19
console.log(personProxy.age);
console.log(person);
```

覆盖了原来默认的执行，每次set或者get的时候哦，会交给代理内部的方法执行。

```bash
set的代理执行
target-->[object Object]
param-->age
newValue-->19
-------------------------------
get的代理执行
target-->[object Object]
param-->age
-------------------------------
undefined
{ name: 'test', age: 18, birth: '2001-01-01' }
```

>想看到`[object Object]`中的内容使用`JSON.stringfy(xxx)`

## 添加额外行为

想要测试某个方法的执行时间，如果直接在方法内部添加计时函数，会让函数中有额外的代码，可以使用代理来对函数进行代理，在代理对象中添加计时的方法。

```javascript
const checkEven = (number) => {
    return checkEven % 2 == 0;
}

const checkEvenProxy = new Proxy(checkEven, {
    apply: (target, thisArg, args) => {
        console.time("checkEven")
        console.log("target-->" + target)
        console.log("thisArg-->" + thisArg);
        console.log("args-->" + args);
        const result = target.apply(thisArg, args)
        console.timeEnd("checkEven")
        return result  // 如果这里没有返回结果，最终的结果将会是undefined
    }
})

console.log(checkEvenProxy(123));
```

输出结果

```bash
target-->(number) => {
    return checkEven % 2 == 0;
}
thisArg-->undefined
args-->123
checkEven: 5.384ms
false
```

## vue中的Proxy行为

```javascript
const vm = new Vue({
    data:{
        message:"xxxx"
    },
    methods:{
        output:()=>{
            alert(this.message)
        }
    }
})
```

正常情况下message是存在data对象中的，所以可以通过data.message来进行访问，如果用`vm`对象进行访问将会返回undefined，但是在vue中，使用vue.message可以返回message中对应的结果，这是因为vm是data对象和methods对象的代理，通过这个代理，可以访问这两个对象的方法，**因为data和methods两个对象同时都被vm对象代理，所以在vm对象中可以通过this关键字访问别的对象中的属性。**