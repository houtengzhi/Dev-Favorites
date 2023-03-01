## [CORS与CSRF在Spring Security中的使用](https://www.cnblogs.com/songjiyang/p/16113846.html)

### 背景
在项目使用了Spring Security之后，很多接口无法访问了，从浏览器的网络调试窗看到的是CORS的报错和403的报错

### 分析
我们先来看一下CORS是什么，和它很相似的CSRF是什么，在SpringSecurity中如何配置以及起的什么作用

#### CORS(Cross Origin Resource Sharing)
CORS跨域资源分享，是一种机制，通过在HTTP响应头中加入特定字段限制不同域的资源请求

跨源HTTP请求的一个例子：运行在 https://domain-a.com 的 JavaScript 代码使用 XMLHttpRequest 来发起一个到 https://domain-b.com/data.json 的请求

出于安全性，浏览器限制脚本内发起的跨源HTTP请求，而CORS就是用来允许跨源请求的

 **用法**
 
用法: 在服务端的响应头（Header）中增加以下字段


|Header|	含义	|例子|
|:---:|---|---|
|Access-Control-Allow-Origin  |  指定了允许访问该资源的外域 URI  |  https://mozilla.org或者*|
|Access-Control-Expose-Headers	|让服务器把允许浏览器访问的头放入白名单 | X-My-Custom-Header, X-Another-Custom-Header  | 
|Access-Control-Max-Age  | 指定了preflight请求的结果能够被缓存多久|	
|Access-Control-Allow-Credentials  |  当浏览器的 credentials 设置为 true 时是否允许浏览器读取 response 的内容  |  true  |
|  Access-Control-Allow-Methods	  |  允许哪些方法  |  GET、POST  |
|  Access-Control-Allow-Methods	  |  允许哪些Header| 	
代码非常简单，有现成的两种方式

**SpringMVC**

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CrossConfig implements WebMvcConfigurer{

	@Override
	public void addCorsMappings( CorsRegistry registry ){

		registry.addMapping( "/**" )
				.allowedOrigins( "*" )
				.allowedMethods( "GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" )
				.allowCredentials( true ).maxAge( 3600 ).allowedHeaders( "*" );
	}
}

```

**Spring Security**
在Spring Security配置类中加入

```java
	@Bean
	public CorsConfigurationSource corsConfigurationSource(){

		CorsConfiguration configuration = new CorsConfiguration();
		configuration.setAllowedOrigins( Collections.singletonList( "*" ) );
		configuration.setAllowedMethods( Arrays.asList( "GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" ) );
		configuration.setAllowCredentials( true );
		configuration.setAllowedHeaders( Collections.singletonList( "*" ) );
		configuration.setMaxAge( 3600L );
		UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration( "/**", configuration );
		return source;
	}

```

**注意**

* 自己写过滤器也行，总之把这些Header加入响应就行
* 上面代码的策略非常宽松，可以适当限制域名保证安全

**Spring Security和SpringMVC的CORS冲突**

Spring Security是用访问认证是过滤器来实现的

SpringMVC的CORS是用拦截器来实现的，参考[SpringBoot中addCorsMappings配置跨域与拦截器互斥问题的原因研究](https://blog.csdn.net/huangyaa729/article/details/103893660)，其中写入响应头的类是`org.springframework.web.cors.DefaultCorsProcessor`

当存在Spring Security时，会存在加不上响应头的现象，因为在过滤器阶段可能因为认证不通过被拒绝了，所以当存在Spring Security的时候使用Spring Security的CORS用法就行

### CSRF(Cross Site Request Forgery)
CSRF跨站请求伪造，是一种web攻击手段，通过向服务器发送伪造请求，进行恶意行为的攻击手段

举个例子，你登录了A网站，浏览器会记录A网站的登录Cookie，下次访问就不会重新登录了，而此时你访问了B网站，网站的网页携带一个恶意JS脚本，其中的内容是获取或更改你A网站的信息，此时浏览器会自动的把Cookie带上，A网站会认为这个请求是你本人的操作，CSRF就是用来防范这种攻击的

#### 如何防范
Spring Security的方式是在表单上面生成一个csrf_token, 提交表单的时候去验证，因为第三方网站是没有这个token的，所以提交不成功

#### 无状态应用
如这篇文章[A Guide to CSRF Protection in Spring Security](https://www.baeldung.com/spring-security-csrf)所说：

> If our stateless API uses token-based authentication, like JWT, we don't need CSRF protection and we must disable it as we saw earlier.

使用JWT无状态应用是不需要csrf保护的，因为JWT不使用cookie， 具体参考[如何通过JWT防御CSRF](https://segmentfault.com/a/1190000003716037)

#### Spring Security关闭csrf
其中的csrf().disable()

```JAVA
@Override
	protected void configure( HttpSecurity http ) throws Exception{

		http
				.cors()
				.and()
				.csrf()
				.disable()
				.sessionManagement()
				.and()
				.authorizeRequests()
				.anyRequest()
				.permitAll()
				.and()
				.exceptionHandling()
				.accessDeniedHandler( new SimpleAccessDeniedHandler() );
	}

```