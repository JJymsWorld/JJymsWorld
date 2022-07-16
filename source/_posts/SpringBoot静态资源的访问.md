---
title: SpringBoot静态资源的访问
tags:
  - [SpringBoot]
categories:
  - gallery
date: 2022-07-16 16:59:48
---

近期在维护一个实验室的项目的时候遇到了不少问题，大致问题就是项目本身原本采用的是前后端打包部署的方式，而我们采用了分离部署的方式，结果是前后端口不一致，产生了许多跨域问题，比如不能发送cookie的session等，导致页面登录状态不能维持。

随后为了了解打包部署研究了一下SpringBoot的静态资源，这里简单记录一下。

#### 静态资源

所谓静态资源就是前端生成的js,css,image,html等文件





下面部分转载自简书，Author：lavor

#### SpringBoot的默认静态资源映射

默认情况下，我们只需要将静态资源放在一下几个目录中就可以直接通过url在浏览器中访问了。

- `/META-INF/resources/`
- `/resources/`
- `/static/`
- `/public/`

<img :src="$withBase('/images/springbootstatic_1.webp')" alt="springbootstatic_1">





如果这四个目录中有相同的静态资源文件，那么优先访问哪个目录下面的资源啊？
 静态资源的默认访问优先级：`/META-INF/resources/`>`/resources/`>`/static/`>`/public/`

在四个目录中都放一个static.html的文件，每个html文件中都说明自己所在的目录，访问结果如下：



<img :src="$withBase('/images/springbootstatic_2.webp')" alt="springbootstatic_2">

SpringBoot关于静态资源的访问涉及到了application.properties中的两个属性：



```csharp
# 默认值为 /*
spring.mvc.static-path-pattern= #这里设置静态资源匹配的url-pattern
# 默认值为 classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/ 
spring.resources.static-locations= #这里设置要指向的路径，多个使用英文逗号隔开，在前面的优先级高
```

此时，我们豁然开朗，知道默认情况下静态资源为什么放在`/META-INF/resources/`、`/resources/`、`/static/`、`/public/`这四个目录了，还有这四个目录访问的优先级是怎么来的了。

修改静态资源映射的方法：

- 我们可以修改这两个属性来改变静态资源的映射，比如我们的所有静态资源都在`mystatic`目录中，并且我们希望访问静态资源的的url都带有`/mystatic/`这个目录前缀:



```cpp
spring.mvc.static-path-pattern=/mystatic/*
spring.resources.static-locations= classpath:mystatic/
```

在`resources`资源目录中创建一个`mystatic`目录，在该目录下面创建一个`static.html`文件，访问结果如下：

<img :src="$withBase('/images/springbootstatic_3.webp')" alt="springbootstatic_3">



- 继承

  ```
  WebMvcConfigurerAdapter
  ```

  类，并且重写

  ```
  addResourceHandlers
  ```

  方法就行，该操作与上面的方法得到的效果是一样的

  <img :src="$withBase('/images/springbootstatic_4.webp')" alt="springbootstatic_4">

**注意：还可以设置外部磁盘目录，设置方式不变，格式如下：`file:d/mystatic/`。**



### WebJars

------

`WebJars`将前端资源（css,js,image,html等等）打包到jar中，然后使用基于`JVM`的包管理器（比如 `Maven`、`Gradle` 等）管理前端依赖的方案。SpringBoot中也可以通过WebJars来访问静态资源。
 SpringBoot默认将`/webjars/**`映射到 `classpath:/META-INF/resources/webjars/`。

- `/webjars/**`：表示`/webjars/`目录下的所有文件，及存在其目录下的jar包中的所有文件。

所以默认情况下我们需要访问`WebJars`中的资源，需要将其jar包放到`classpath:/META-INF/resources/webjars/`目录中。
 我们来使用一下`WebJars`:

- 在`pom.xml`中引入`jquery`的`WebJars`，默认会被放在`classpath:/META-INF/resources/webjars/`目录中



```xml
 <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>jquery</artifactId>
      <version>2.1.1</version>
  </dependency>
```

- 在前端`webjars.html`页面中引入`jquery`



```xml
<script src="/webjars/jquery/2.1.1/jquery.js"></script>
```

- 访问页面

  ```
  webjars.html
  ```

  ，查看源码，可以链接到

  ```
  jquery.js
  ```

  的内容

  <img :src="$withBase('/images/springbootstatic_5.webp')" alt="springbootstatic_5">

#### 版本号统一管理

------

如果我们有很多页面都是用了`WebJars`中的资源，而我们现在要升级`WebJars`的版本，岂不是要在每个页面中都改动一下，这样很麻烦啊，有没有简单的方法啊。此时，我们可以进行版本号统一管理。

- 在`pom.xml`中引入`jquery`的`WebJars`的基础上添加：



```xml
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>webjars-locator</artifactId>
        </dependency>
```

- 添加版本号统一管理的控制器

  <img :src="$withBase('/images/Springbootstatic_6.webp')" alt="Springbootstatic_6">

- 在页面中引入`jquery`的方式如下：



```xml
<script src="/webjarslocator/jquery/jquery.js"></script>
```

## 静态资源版本管理

------

当我们资源内容发生变化时，由于浏览器缓存，用户本地的静态资源还是旧的资源，为了防止这种情况导致的问题，我们可能会手动在请求url的时候加个版本号或者其他方式。



```xml
<script type="text/javascript" src="/lavor.js?v=1.1"></script>
```

SpringMVC提供了两种方式可以帮助我们很容易地解决这类问题。

### MD5方式

------

- 修改`application.properties`配置文件



```bash
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```

- 添加

  ```
  @ControllerAdvice
  ```

  注解，返回

  ```
  ResourceUrlProvider
  ```

  的对象，这样所有通过控制器返回的模板页面（jsp,thymeleaf,freeemarker等）就都可以在页面中使用该对象了

  <img :src="$withBase('/images/springbootstatic_7.webp')" alt="springbootstatic_7">

- 在控制器返回的模板页面中，添加以下信息，我们这里的返回页面时jsp页面，`lavor.js`是`webapp`目录下面的js脚本文件。



```xml
<script  src="${urls.getForLookupPath('/lavor.js') }"></script>
```

- 通过浏览器访问控制器的请求url，查看页面源代码



```xml
<script  src="/lavor-fdfa0502716d517c6cad4f2536aa02a1.js"></script>
```

请求`/lavor-fdfa0502716d517c6cad4f2536aa02a1.js`，我们MD5配置的`paths=/**`，所以SpringMVC会尝试url中是否包含`-`，如果包含会去掉后面这部分，然后去映射的目录（如webapp根目录，上面提到的四大静态映射目录）查找`/lavor.js`文件，如果能找到就返回。

### 版本号方式

------

- 修改`application.properties`配置文件，**注意MD5方式中的是`content`，这里是`fixed`**



```csharp
spring.resources.chain.strategy.fixed.enabled=true
#版本号处理的路径
spring.resources.chain.strategy.fixed.paths=/**
# 版本号，可以为所处理路径中的资源加上/v1.1目录前缀
spring.resources.chain.strategy.fixed.version=v1.1
```

- 添加

  ```
  @ControllerAdvice
  ```

  注解，返回

  ```
  ResourceUrlProvider
  ```

  的对象，这样所有通过控制器返回的模板页面（jsp,thymeleaf,freeemarker等）就都可以在页面中使用该对象了

  <img :src="$withBase('/images/springbootstatic_7.webp')" alt="springbootstatic_7">

- 在控制器返回的模板页面中，添加以下信息，我们这里的返回页面时jsp页面，`lavor.js`是`webapp`目录下面的js脚本文件.



```xml
<script  src="${urls.getForLookupPath('/lavor.js') }"></script>
```

- 通过浏览器访问控制器的请求url，查看页面源代码



```xml
<script  src="/v1.1/lavor.js"></script>
```

请求`/v1.1/lavor.js`，会查看`v1.1`是不是版本号，如果是就去掉前缀目录，直接查找`/lavor.js`。

**注意：我们发现如果添加了webapp目录，那么该目录也可以存放静态资源，并且默认情况下访问优先级比`/META-INF/resources/`还要高。**

