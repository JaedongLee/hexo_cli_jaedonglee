---
title: Java-Note-记一次log4j配置文件无法加载问题的解决过程
date: 2020-04-27 17:56:00
tags: log4j
category: Java
---

## 起因

在一次由于其他原因更改了`POM`文件配置的操作后发现: `spring boot`项目中配置的`log4j2.yml`文件无法正常加载, 显示错误如下:

> ERROR StatusLogger No log4j2 configuration file found. Using default configuration: logging only errors to the console.

## 分析过程

发现在更改`POM`文件过程中删除了几个依赖. 其中有一个是:

```maven
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-yaml</artifactId>
</dependency>
```

进入`com.fasterxml.jackson.dataformat.yaml`包中, 发现与解析`yml`文件相关的类`YAMLParser`, `debug`该类的构造器, 发现`org.apache.logging.log4j.core`包中的`JsonConfigurationFactory`对象初始化时会加载`com.fasterxml.jackson.dataformat.yaml`包中的类. 具体代码如下:

```java
public class JsonConfigurationFactory extends ConfigurationFactory {
    ...
    private static final String[] dependencies = new String[] {
            "com.fasterxml.jackson.databind.ObjectMapper",
            "com.fasterxml.jackson.databind.JsonNode",
            "com.fasterxml.jackson.core.JsonParser"
    };

    private final boolean isActive;

    // 尝试加载com.fasterxml.jackson.dataformat.yaml包中的dependencies类, 如果失败, 将isActive置false
    public JsonConfigurationFactory() {
        for (final String dependency : dependencies) {
            if (!Loader.isClassAvailable(dependency)) {
                LOGGER.debug("Missing dependencies for Json support");
                isActive = false;
                return;
            }
        }
        isActive = true;
    }
    ...
    // 如果isActive置false, 则返回null, 使用default configuration
    public Configuration getConfiguration(final ConfigurationSource source) {
        if (!isActive) {
            return null;
        }
        return new JsonConfiguration(source);
    }
    ...
}

public abstract class ConfigurationFactory extends ConfigurationBuilderFactory {
    ...
    private static class Factory extends ConfigurationFactory {
        ...
        public Configuration getConfiguration(final String name, final URI configLocation) {
            ...
            Configuration config = getConfiguration(true, name);
            if (config == null) {
                config = getConfiguration(true, null);
                if (config == null) {
                    config = getConfiguration(false, name);
                    if (config == null) {
                        // 从这里进入上面的getConfiguration方法
                        config = getConfiguration(false, null);
                    }
                }
            }
            if (config != null) {
                return config;
            }
            // 此处为起因中的语句
            LOGGER.error("No log4j2 configuration file found. Using default configuration: logging only errors to the console.");
            return new DefaultConfiguration();
        }
    }
}
```

由上面的代码可知, 当无法加载`com.fasterxml.jackson.dataformat.yaml`包中的类时, 会弹出起因中的错误

## 解决方案

在`POM`文件中增加`com.fasterxml.jackson.dataformat.yaml`依赖

## 总结

遇到这种框架问题, 可以尝试初步定位问题原因, 再通过`debug`框架源码来精确定位问题所在位置从而找到解决方案
