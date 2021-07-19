# Spring在创建Bean的过程中分为三步

1.  实例化，对应方法：`AbstractAutowireCapableBeanFactory`中的`createBeanInstance`方法
    
2.  属性注入，对应方法：`AbstractAutowireCapableBeanFactory`的`populateBean`方法
    
3.  初始化，对应方法：`AbstractAutowireCapableBeanFactory`的`initializeBean`

# 循环依赖解决

`getSingleton(beanName, true)`这个方法实际上就是到缓存中尝试去获取Bean，整个缓存分为三级

1.  `singletonObjects`，一级缓存，存储的是所有创建好了的单例Bean
    
2.  `earlySingletonObjects`，完成实例化，但是还未进行属性注入及初始化的对象
    
3.  `singletonFactories`，提前暴露的一个单例工厂，二级缓存中存储的就是从这个工厂中获取到的对象
    

因为A是第一次被创建，所以不管哪个缓存中必然都是没有的，因此会进入`getSingleton`的另外一个重载方法`getSingleton(beanName, singletonFactory)`。