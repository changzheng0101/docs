# 异步请求

传统的异步请求处理方式是在最后添加回调函数，但是过多的callback将会导致回调地狱(callback hell，也就是多个callback一直嵌套)

>异步的核心目的就是为了不要阻塞主线程

## 任务

假设现在要完成以下几个任务

1. 从dog.txt中读取文件
2. 根据读取的文件发起请求
3. 将请求结果的`message`写入result.txt中

## callback

该种写法会导致回调地狱，也就是很多个嵌套的callback。

```js
const fs = require('fs');
const superagent = require('superagent');

fs.readFile('./dog.txt', 'utf-8', (err, data) => {
    console.log(data);
    superagent.get(`https://dog.ceo/api/breed/${data}/images/random`, (err, data) => {
        if (err) console.log(err.message);
        console.log(data.body.message);
        fs.writeFile('./result.txt', data.body.message, (err) => {
            if (err) console.log(err);
        })
    })
})
```

>这种方法写的代码可读性很差，可以看到前面出现了一个类似于 “三角形”的缩进。这种方式不会阻塞主线程。

## Promise

需要先创建对应的Promise，每个Promise定义两个方法`reslove`和`reject`，前者用于解决返回的数据，后者用于处理错误。

```js
const fs = require('fs');
const superagent = require('superagent')

const readPromise = file => {
    return new Promise((resolve, reject) => {
        fs.readFile(file, 'utf-8', (err, data) => {
            if (err) reject('can not find  file 😢');
            resolve(data);
        })
    });
}

const writePromise = (file, data) => {
    return new Promise((resolve, reject) => {
        fs.writeFile(file, data, (err) => {
            if (err) reject('can not write to file');
            resolve('success');
        })
    })
}

readPromise(`${__dirname}/dog.txt`)
    .then(data => {
        console.log(`I get data for ${data}`);
        return superagent.get(`https://dog.ceo/api/breed/${data}/images/random`)
    })
    .then(res => {
        console.log(`get result is ${res.body.message}`);
        return writePromise(`${__dirname}/result.txt`, res.body.message);
    })
    .then(data => {
        console.log(`write to file ${data}!`);
    })
    .catch(err => {
        console.log(err);
    })
```

>多个`then`然后一个`catch`，代码可以读性增加，类似于定义了一个处理流程。

## async&await

二者一起使用，可以让代码写法类似于synchronize的写法，但是实际上是asynchronize的实现，当函数体中使用await之后，外界的函数必须用async进行修饰。返回的也是一个Promise。

这两个关键字也要和Promise结合使用。

```js
const fs = require('fs')
const superagent = require('superagent')

// 创建两个Promise
const readPromise = file => {
    return new Promise((resolve, reject) => {
        fs.readFile(file, 'utf-8', (err, data) => {
            if (err) reject('can not find file 😢');
            resolve(data);
        })
    });
}

const writePromise = (file, data) => {
    return new Promise((resolve, reject) => {
        fs.writeFile(file, data, (err) => {
            if (err) reject('can not write to file');
            resolve('success');
        })
    })
}

// 只请求一个资源的简单实现，try/catch方法可以完成错误的处理
const getDogPic = async function () {
    try {
        const dogName = await readPromise(`${__dirname}/dog.txt`);
        console.log(`dogName ---> ${dogName}`);

        const res = await superagent.get(`https://dog.ceo/api/breed/${dogName}/images/random`)
        console.log(`res data: ${res.body.message}`);

        await writePromise(`${__dirname}/result.txt`, res.body.message);
        console.log('write success');
    } catch (error) {
        console.log(error);
        // throw之后外界才可以捕获
        throw error + ' out';
    }
    return '2:READY 🐶';
}

const getMultiDogPic = async function () {
    try {
        const dogName = await readPromise(`${__dirname}/dog.txt`);
        console.log(`dogName ---> ${dogName}`);
		
        // 申请多个资源时，如果使用await关键字，只有等完某一个Promise，下一个才会执行，浪费时间
        const res1Pro = superagent.get(`https://dog.ceo/api/breed/${dogName}/images/random`)
        const res2Pro = superagent.get(`https://dog.ceo/api/breed/${dogName}/images/random`)
        const res3Pro = superagent.get(`https://dog.ceo/api/breed/${dogName}/images/random`)

        const all = await Promise.all([res1Pro, res2Pro, res3Pro]);
        const imgs = all.map(el => el.body.message);
        console.log(`res data: ${imgs}`);

        await writePromise(`${__dirname}/result.txt`, imgs.join('\n'));
    } catch (err) {
        console.log(err);
        throw err;
    }
    return '2:READY 🐶';
}


// console.log('1: Will get dog pics!');
// getDogPic()
//     .then(data => {
//         console.log(data);
//         console.log('3: Done getting dog pics!');
//     })
//     .catch(err => {
//         console.log(err);
//     })

// (async () => {
//     console.log('1: Will get dog pics!');
//     const result = await getDogPic()
//     console.log(result);
//     console.log('3: Done getting dog pics!');
// })()

console.log('1: Will get dog pics!');
getMultiDogPic()
    .then(data => {
        console.log(data);
        console.log('3: Done getting dog pics!');
    })
    .catch(err => {
        console.log(err);
    })
```

