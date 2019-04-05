#### 今天就来跟着源码了解一下security的基本运行原理  

1. 引入依赖  
```  
    <!--Spring Security Oauth2相关-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-oauth2</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>  
```  
2. demo  
```  
  @GetMapping(value = "/hello")
	public String hello() {
		return "hello world";
	}  
```  

在不进行其他自定义配置的情况下，运行并访问该方法时，会弹出Http Basic默认认证框，SpringSecurity默认的固定用户名为：user，密码则在控制台打印输出：  
Using default security password: fb39f959-fe9c-4d51-abb8-7303cfba4d30  
输入完成后，则请求到该方法被得到响应，那么我们来分析一下这其中的运行机制：  

从这个例子里我们可以看到,在默认的情况下,不做任何配置的时候，Spring Security做了这么两件事：  
>1. Spring Security将我们所有的服务都保护起来了,任何一个Rest服务,要想调用都要先进行身份认证。  
>2. 份认证的方式就是上图中的http basic的方式,这个是Spring Security默认的一个行为。  

