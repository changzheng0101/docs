# Promise

一般Promise用于异步实现

```javascript
const makeServerRequest = new Promise((resolve, reject) => {
  // responseFromServer 设置为 false，表示从服务器获得无效响应
  let responseFromServer = false;
  if(responseFromServer) {
    resolve("We got the data");
  } else {  
    reject("Data not received");
  }
});

// 成功之后会调用这个方法 result是传入resolve的参数
makeServerRequest.then(result => {
  console.log(result);
});

// 失败调用这个方法 error是传入reject的参数
makeServerRequest.catch(error =>{
    console.log(error);
})
```

