# 在WebSocket中如何使用Redis

## 1.问题描述

在Controller 和 service中都可以正常使用Redis，在WebSocket中却报错？

![image-20240128172119816](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240128172119816.png)

![image-20240128172148207](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20240128172148207.png)

初次遇到这个问题，我还以为是字符串类型错误，改了两个小时才发现问题所在！！！

## 2.原因

在@ServerEndpoint注解类中使用@Resource或@Autowired注入失败。报出空指针异常。

原因是WebSocket是线程安全的，有用户连接时就会创建一个新的端点实例，一个端WebSocket是多对象的，使用的spring却是单例模式。这两者刚好冲突。

@Autowired注解注入对象是在启动的时候就把对象注入，而不是在使用A对象时才把A需要的B对象注入到A中。

而WebSocket在刚刚有说到，有连接时才实例化对象，而且有多个连接就有多个对象。总结就是，WebSocket是多对象的。不管单独使用也好，结合spring也好，或者结合SpringBoot也罢，他都是多对象的。

## 3.解决步骤

### 1、新建一个SpringUtil.java类，通过getBean的方法主动获取实例

~~~java
package com.example.utils;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.stereotype.Component;

@Component
public final class SpringUtils implements BeanFactoryPostProcessor {

    private static ConfigurableListableBeanFactory beanFactory;

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        SpringUtils.beanFactory = beanFactory;
    }

    public static ConfigurableListableBeanFactory getBeanFactory() {
        return beanFactory;
    }

    /**
     * 获取对象
     *
     * @param name
     * @return Object 一个以所给名字注册的bean的实例
     * @throws org.springframework.beans.BeansException
     */
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String name) throws BeansException {
        if (getBeanFactory() == null) {
            //zhengkai.blog.csdn.net
            System.out.println("本地调试Main模式，没有BeanFactory，忽略错误");
            return null;
        } else {
            T result = (T) getBeanFactory().getBean(name);
            return result;
        }
    }

    /**
     * 获取类型为requiredType的对象
     *
     * @param name
     * @return
     * @throws org.springframework.beans.BeansException
     */
    public static <T> T getBean(Class<T> name) throws BeansException {
        if (getBeanFactory() == null) {
            //zhengkai.blog.csdn.net
            System.out.println("本地调试Main模式，没有BeanFactory，忽略错误");
            return null;
        } else {
            T result = (T) getBeanFactory().getBean(name);
            return result;
        }
    }

    /**
     * 如果BeanFactory包含一个与所给名称匹配的bean定义，则返回true
     *
     * @param name
     * @return boolean
     */
    public static boolean containsBean(String name) {
        return getBeanFactory().containsBean(name);
    }

    /**
     * 判断以给定名字注册的bean定义是一个singleton还是一个prototype。 如果与给定名字相应的bean定义没有被找到，将会抛出一个异常（NoSuchBeanDefinitionException）
     *
     * @param name
     * @return boolean
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     */
    public static boolean isSingleton(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().isSingleton(name);
    }

    /**
     * @param name
     * @return Class 注册对象的类型
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     */
    public static Class<?> getType(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().getType(name);
    }

    /**
     * 如果给定的bean名字在bean定义中有别名，则返回这些别名
     *
     * @param name
     * @return
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     */
    public static String[] getAliases(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().getAliases(name);
    }

}


~~~

### 3.在WebSocketSingleServer.java中导入

~~~java
    @Resource
    private StringRedisTemplate stringRedisTemplate= SpringUtils.getBean(StringRedisTemplate.class);
~~~



