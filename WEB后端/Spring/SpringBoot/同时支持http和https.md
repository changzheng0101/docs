# 同时支持http和https

很多情况下，需要同时开放http和https，要不本地调试的时候非常的不方便。

## 开启https

```yaml
server:
  port: 8443
  ssl:
    key-store-password: password   #填写pfx-password.txt文件内的密码。
    key-store-type: PKCS12
    key-store: classpath:xxx.pfx    #您需要使用实际的证书名称替换domain name.pfx。
```

>classpath之后的东西放在`resources`目录下即可

生成一个可以访问这个https端口的restTemplate

```properties
#trust store location
trust.store=classpath:xxx.pfx  
#trust store password
trust.store.password=password
```

```java
RestTemplate restTemplate() throws Exception {
    SSLContext sslContext = new SSLContextBuilder()
      .loadTrustMaterial(trustStore.getURL(), trustStorePassword.toCharArray())
      .build();
    SSLConnectionSocketFactory socketFactory = new SSLConnectionSocketFactory(sslContext);
    HttpClient httpClient = HttpClients.custom()
      .setSSLSocketFactory(socketFactory)
      .build();
    HttpComponentsClientHttpRequestFactory factory = 
      new HttpComponentsClientHttpRequestFactory(httpClient);
    return new RestTemplate(factory);
}
```

## 开启https之后仍然开放http

假设http还需要开放8000端口

在`application.properties`中添加如下声明：

```properties
server.http.port=8080
```

之后还需要创建另一个Tomcat connector

```java
@Configuration
public class AppConfiguration {
    @Value("${server.http.port}")
    private int httpPort;
    
    @Bean
    public WebServerFactoryCustomizer<TomcatServletWebServerFactory> cookieProcessorCustomizer() {
        return (TomcatServletWebServerFactory factory) -> {
            // also listen on http
            final Connector connector = new Connector();
            connector.setPort(httpPort);
            factory.addAdditionalTomcatConnectors(connector);
        };
    }
}
```

至此，通过8443可以访问https，通过8000可以访问http端口。