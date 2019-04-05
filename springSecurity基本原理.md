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

以上简单的使用，并不足以达到我们的要求，那么如何覆盖SpringSecurity默认行为呢？  
先举个例子： 我们可以提供一个自定义的表单登录  
具体做法：  
    >1. 创建一个类 extends WebSecurityConfigurerAdapter(web安全应用配置的适配器)，可override configure(); 该方法共有三种形式：  
    
```    
            @Configuration
            public class WebConfig extends WebSecurityConfigurerAdapter {
                
                // 接收AuthenticationManagerBuilder的方法：
                protected void configure(AuthenticationManagerBuilder auth) throws Exception {
                    this.disableLocalConfigureAuthenticationBldr = true;
                }
                
                // 接收WebSecurity的方法：
                public void configure(WebSecurity web) throws Exception {
                }  
                
                // 我们重点关注接收参数为HttpSecurity的configure方法，可以看到，它的默认配置正是：
                // 这段默认代码则体现出：默认情况下Spring Security应用的所有请求都需要经过认证，并且认证方式为Http Basic。
                protected void configure(HttpSecurity http) throws Exception {
                    http
                        .authorizeRequests()
                            .anyRequest().authenticated()
                            .and()
                        .formLogin().and()
                        .httpBasic();
                }
            }  
```  
现在我们需要覆盖掉HttpSecurity的方法，因为我们想要自定义一个form表单登录。  
例如：  
```  
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //启用基于表单形式的登录
        http.formLogin()
            .and()
            //下面的配置都是关于授权的配置
            .authorizeRequests()
            //任何请求
            .anyRequest()
            //都需要认证
            .authenticated();
    }  
```  
安全其实就是两件事：一个是认证，一个是授权。  
这时，重启系统，再次访问http://localhost:8080/hello 就会发现跳到了一个表单登录页  同样输入固定用户名：user 和 系统随机产生的pwd实现认证，认证成功后**重定向**到之前的请求，这个也是Spring Security默认的登录成功处理器的一个行为。  

### 在了解了基本配置后，开始谈Security基本原理：  




