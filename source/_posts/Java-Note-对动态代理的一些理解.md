---
title: Java-Note-对动态代理的一些理解
date: 2018-09-05 16:28:46
tags:
category: Java
---

动态代理可以动态的创建代理并动态地处理对所代理方法的调用. 在动态代理上所做的所有调用都会被重定向到单一的`调用处理器`(`InvocationHandler`)上.        

动态代理的例子如下:

```
class DynamicProxyHandler implements InvocationHandler {
    private Object proxied;

    public DynamicProxyHandler(Object proxied) {
        this.proxied = proxied;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("*** proxy: " + proxy.getClass() + ", method: " + method + ", args: " + args);
        if (args != null) {
            for (Object arg : args) {
                System.out.println("  " + arg);
            }
        }
        Object result = method.invoke(proxied, args);
        System.out.println("proxy info: " + proxy.toString());
        return result;
    }
}

public class SimpleDynamicProxy {
    public static void consumer(Interface iface) {
        iface.doSomething();
        iface.somethingElse("bonobo");
    }

    public static void main(String[] args) {
        RealObject real = new RealObject();
        consumer(real);
        Interface proxy = (Interface) Proxy.newProxyInstance(
                Interface.class.getClassLoader(),
                new Class[]{Interface.class},
                new DynamicProxyHandler(real)
        );
        consumer(proxy);
    }
}
```

在例子中调用proxy的方法如`doSomething()`会被指向`invoke()`, 而如果在`invoke()`中再调用proxy的方法如`toString()`时,会陷入循环调用的陷阱, 进而抛出`Stack Overflow`的错误.        

参考文献: Thinking in Java Fourth Edition