---
title: Java-Note-反射原理
date: 2018-04-17 09:06:09
tags: Reflection
category: Java
---
1. 通过Class类的getDeclaredMethod可以获取指定方法名和参数的方法对象Method
    ```
    public Method getDeclaredMethod(String name, Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {
        checkMemberAccess(Member.DECLARED, Reflection.getCallerClass(), true);
        Method method = searchMethods(privateGetDeclaredMethods(false), name, parameterTypes);
        if (method == null) {
            throw new NoSuchMethodException(getName() + "." + name + argumentTypesToString(parameterTypes));
        }
        return method;
    }
    ```
    2. 其中privateGetDeclaredMethods方法从缓存或JVM中获取该Class中声明的方法列表,<!-- more -->
        ```
        private Method[] privateGetDeclaredMethods(boolean publicOnly) {
            checkInitted();
            Method[] res;
            ReflectionData<T> rd = reflectionData();
            if (rd != null) {
                res = publicOnly ? rd.declaredPublicMethods : rd.declaredMethods;
                if (res != null) return res;
            }
            // No cached value available; request value from VM
            res = Reflection.filterMethods(this, getDeclaredMethods0(publicOnly));
            if (rd != null) {
                if (publicOnly) {
                    rd.declaredPublicMethods = res;
                } else {
                    rd.declaredMethods = res;
                }
            }
            return res;
        }
        ```
        1. 其中reflectionData()方法的实现如下:
            ```
            private ReflectionData<T> reflectionData() {
                SoftReference<ReflectionData<T>> reflectionData = this.reflectionData;
                int classRedefinedCount = this.classRedefinedCount;
                ReflectionData<T> rd;
                if (useCaches &&
                    reflectionData != null &&
                    (rd = reflectionData.get()) != null &&
                    rd.redefinedCount == classRedefinedCount) {
                    return rd;
                }
                // else no SoftReference or cleared SoftReference or stale ReflectionData
                // -> create and replace new instance
                return newReflectionData(reflectionData, classRedefinedCount);
            }
            ```
            有一个比较重要的数据结构ReflectionData, 用来缓存从JVM中读取的属性数据:
            ```
            private static class ReflectionData<T> {
                volatile Field[] declaredFields;
                volatile Field[] publicFields;
                volatile Method[] declaredMethods;
                volatile Method[] publicMethods;
                volatile Constructor<T>[] declaredConstructors;
                volatile Constructor<T>[] publicConstructors;
                // Intermediate results for getFields and getMethods
                volatile Field[] declaredPublicFields;
                volatile Method[] declaredPublicMethods;
                volatile Class<?>[] interfaces;

                // Value of classRedefinedCount when we created this ReflectionData instance
                final int redefinedCount;

                ReflectionData(int redefinedCount) {
                    this.redefinedCount = redefinedCount;
                }
            }
            ```
            从reflectionData()的代码可以看出, reflectionData对象是SoftReference类型的, 说明在内存紧张时会被回收, 如果在被回收后又执行反射方法, 则会用newReflectionData方法产生一个新的对象
                1. newReflectionData()方法: 
                    1. 如果`userCache`为空, 返回null
                    2. 否则循环尝试通过unsafe.compareAndSwapObject()方法重新设置reflectionData
        2. 在privateGetDeclaredMethods方法中, 如果通过reflectionData()获得的ReflectionData对象不为空, 则尝试从ReflectionData对象中获取declaredMethods属性, 如果为空, 比如第一次或被GC回收之后(疑问), 会从JVM中重新获取一次, 并赋值给ReflectionData, 下次调用时可用缓存数据
    3. 其中searchMethods从获取的方法列表中找到匹配名称和参数的方法对象
2. Method调用
    1. 获取到指定的方法对象Method之后, 就可以调用invoke()方法, 实现如下:
    ```
    @CallerSensitive
    public Object invoke(Object obj, Object... args)
        throws IllegalAccessException, IllegalArgumentException,
           InvocationTargetException
    {
        if (!override) {
            if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
                Class<?> caller = Reflection.getCallerClass();
                checkAccess(caller, clazz, obj, modifiers);
            }
        }
        MethodAccessor ma = methodAccessor;             // read volatile
        if (ma == null) {
            ma = acquireMethodAccessor();
        }
        return ma.invoke(obj, args);
    }
    ```
    要注意: 这里的MethodAccessor对象是invoke方法实现的关键, 一开始ma为空, 此时调用acquireMethodAccessor()获得新的MethodAccessor对象
    1. acquireMethodAccessor()方法中, 会通过ReflectionFactory类的newMethodAccessor创建一个实现了MethodAccessor接口的对象
    2. 在ReflectionFactory类中有两个重要的静态变量: 
        3. noInflation(默认false): 允许切换至Java代码实现的MethodAccessorImpl对象
        4. inflationThreshold: 触发调用Java代码实现的MethodAccessorImpl对象的次数
    5. newMethodAccessor会返回DelegatingMethodAccessorImpl对象, 而DelegatingMethodAccessorImpl只是一个代理类, 被代理的对像delegate开始是指向的NativeMethodAccessorImpl对象, 所以最终Method的invoke()方法调用的是NativeMethodAccessorImpl对象的invoke方法
        ```
        class NativeMethodAccessorImpl extends MethodAccessorImpl {
            private final Method method;
            private DelegatingMethodAccessorImpl parent;
            private int numInvocations;

            NativeMethodAccessorImpl(Method var1) {
                this.method = var1;
            }

            public Object invoke(Object var1, Object[] var2) throws IllegalArgumentException, InvocationTargetException {
                    if (++this.numInvocations > ReflectionFactory.inflationThreshold() && !ReflectUtil.isVMAnonymousClass(this.method.getDeclaringClass())) {
                        MethodAccessorImpl var3 = (MethodAccessorImpl)(new MethodAccessorGenerator()).generateMethod(this.method.getDeclaringClass(), this.method.getName(), this.method.getParameterTypes(), this.method.getReturnType(), this.method.getExceptionTypes(), this.method.getModifiers());
                        this.parent.setDelegate(var3);
                    }

                    return invoke0(this.method, var1, var2);
                }

                void setParent(DelegatingMethodAccessorImpl var1) {
                    this.parent = var1;
                }

                private static native Object invoke0(Method var0, Object var1, Object[] var2);
        }
        ```
    在这里用到了inflationThreshold,  当调用了15次方法后, 会将delegate对象设置为专门生成的的MethodAccessor实现类, 生成其中的invoke()方法的字节码