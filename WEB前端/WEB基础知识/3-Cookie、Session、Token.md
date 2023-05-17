# Cookie、Session、Token

## Cookie

cookie保存在浏览器中，可以用于认证用户，当用户第一登录之后，将用户的认证信息保存在Cookie中，之后用户每次访问都可以携带这个Cookie来进行访问。

```java
Cookie[] cookies = req.getCookies(); //获得Cookie
cookie.getName(); //获得cookie中的key
cookie.getValue(); //获得cookie中的vlaue
new Cookie("lastLoginTime", System.currentTimeMillis()+""); //新建一个cookie
cookie.setMaxAge(24*60*60); //设置cookie的有效期
resp.addCookie(cookie); //响应给客户端一个cookie
```

可以给cookie设置有效期为0来达到删除cookie的效果。

每次访问都可以给用户返回很多个cookie，也可以读取这些cookie。

cookie通过key:value来保存数据，但是cookie的key和value都必须为String。

## Session

浏览器从打开到关闭为一次会话Session，一般客户端只会存储SessionID，具体的Session存储在服务器上，当连接的浏览器过多时，服务器上存储的Session也会过多，给服务器带来很大的负担。

```java
//得到Session 存储数据
HttpSession session = req.getSession();
//给Session中存东西
session.setAttribute("name",new Person("秦疆",1));
//获取Session的ID
String sessionId = session.getId();


//得到Session 获取数据
HttpSession session = req.getSession();
Person person = (Person) session.getAttribute("name");
System.out.println(person.toString());
HttpSession session = req.getSession();
session.removeAttribute("name");
//手动注销Session
session.invalidate();
```

session是通过key：value的方式来保存的，value为Object

## Token

token是在服务器上保存秘钥，然后将数据放在以JWT方式，放在Cookie中发送给客户端。之后客户端每次访问服务器，都需要带上Cookie，服务器通过秘钥，完成解码，获取所需要的数据。

>Cookie是一种在浏览器和服务器之间传递信息的方式；Session保存会话，Session由服务器产生，并且保存在服务器，与客户端沟通时只传递SessionID；Token方式，服务器端只保存秘钥，具体的内容发送给客户端，客户端每次访问的时候带着Token来，之后服务器进行解码，大大降低了服务器端的存储压力。