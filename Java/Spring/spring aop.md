# 配置启用spring aop

1. 使用@EnableAspectJAutoProxy注解

```java
@Import(AspectJAutoProxyRegistrar.class)  
public @interface EnableAspectJAutoProxy {
	...
}
```

原理是在启动过程中，载入`AspectJAutoProxyRegistrar`对象。

```java
class AspectJAutoProxyRegistrar implements ImportBeanDefinitionRegistrar{
}
```

`AnnotationAwareAspectJAutoProxyCreator`通过`AopConfigUtils`注册到`beanFactory`(`DefaultListableBeanFactory`)的`BeanPostProcessor`当中



