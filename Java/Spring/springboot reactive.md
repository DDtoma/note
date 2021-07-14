# 在springboot中使用webflux

## 配置webflux
1. 引入jar包

```xml
<dependency>  
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-webflux</artifactId>  
</dependency>
```

2. 启动类主动配置

```java
@SpringBootApplication  
@RestController  
public class App {
    @GetMapping("/")  
    public String index() {  
        return "hello spring";  
    }  
  
    public static void main( String[] args ) {  
        SpringApplication springApplication = new SpringApplication(App.class);  
        springApplication.setWebApplicationType(WebApplicationType.REACTIVE);  
        ConfigurableApplicationContext applicationContext = springApplication.run(args);  
    }  
}
```

如果只是单独引入spring-boot-starter-webflux不需要手动设置webApplictionType，当同时引入spring-boot-starter-web和spring-boot-starter-webflux时需要手动设置webApplictionType不然会走默认类型servlet。

### 配置过程解析
1. SpringApplication的构造函数会设置`webApplicationType`的类型
```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
	...
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
	...
}
```

2. `WebApplicationType.deduceFromClasspath()`默认使用servlet
```java
static WebApplicationType deduceFromClasspath() {  
   if (ClassUtils.isPresent(WEBFLUX_INDICATOR_CLASS, null) && !ClassUtils.isPresent(WEBMVC_INDICATOR_CLASS, null)  
         && !ClassUtils.isPresent(JERSEY_INDICATOR_CLASS, null)) {  
      return WebApplicationType.REACTIVE;  
 }  
   for (String className : SERVLET_INDICATOR_CLASSES) {  
      if (!ClassUtils.isPresent(className, null)) {  
         return WebApplicationType.NONE;  
 }  
   }  
   return WebApplicationType.SERVLET;  
}

```

3. 构造`ConfigurableApplicationContext`的过程中判断`webApplictionType`的类型
```java
public ConfigurableApplicationContext run(String... args) {
	...
	try {
		context = createApplicationContext();
	} (Throwable ex) {
	}
}
```

```java
protected ConfigurableApplicationContext createApplicationContext() {  
   Class<?> contextClass = this.applicationContextClass;  
 if (contextClass == null) {  
      try {  
         switch (this.webApplicationType) {  
         case SERVLET:  
            contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);  
 break; case REACTIVE:  
            contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);  
 break; default:  
            contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
 }  
      }  
      catch (ClassNotFoundException ex) {  
         throw new IllegalStateException(  
               "Unable create a default ApplicationContext, please specify an ApplicationContextClass", ex);  
 }  
   }  
   return (ConfigurableApplicationContext) BeanUtils.instantiateClass(contextClass);  
}
```

## webfux配置cors
```java
@Configuration
@EnableWebFlux
public class WebConfig implements WebFluxConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {

        registry.addMapping("/api/**")
            .allowedOrigins("https://domain2.com")
            .allowedMethods("PUT", "DELETE")
            .allowedHeaders("header1", "header2", "header3")
            .exposedHeaders("header1", "header2")
            .allowCredentials(true).maxAge(3600);

        // Add more mappings...
    }
}
```
