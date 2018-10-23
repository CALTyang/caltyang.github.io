---
title: 记一个模块化REST接口的思路
date: 2018-07-13 15:22:41
tags: Java
---
最近遇到了这样一个场景：
> 之前有一个项目，项目中实现了一个天气预报的接口，当然，就是调用网上的API。然后又开了一个新项目，同样有天气预报这样一个功能。

上面是大前提，其实简单的可以直接复制粘贴代码，再从头写一个接口，一样的逻辑就行了。但是突然就在想，是否存在这样一个方式，我可以将接口，像Maven依赖一样，进行模块化? 因为是完全一样的功能，比如天气预报，接口格式、地址（除了baseUrl）、传参类型，其实完全都是一致的。然后可以达到这样一个效果: 我可以简单的通过配置Maven的pom.xml，然后配置一下开启该模块，就可以将接口注入到项目中，不用写一行实现逻辑！？
<!--more-->

## 0x01 阅读DispatcherServlet源码
---
参考网上DispatcherServlet的源码分析，我们知道DispatcherServlet初始化过程中，有一步是进行了9大组件的初始化操作，代码在initStrategies中：
```java
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
```
用过SpringMVC的肯定知道，我们写一个接口，其实就是写一个Controller类，然后通过@RequestMapping（或者新版本中提供的@GetMapping、@PostMapping）等注解来修饰一个方法，即可产生一个URL的匹配Pattern，以及对应的Handler来处理实际逻辑，所以通过关键字，我们能很快定位到，我们这个需求，可能和initHandlerMappings有关，其源码如下：
```java
private void initHandlerMappings(ApplicationContext context) {
		this.handlerMappings = null;

		if (this.detectAllHandlerMappings) {
			// Find all HandlerMappings in the ApplicationContext, including ancestor contexts.
			Map<String, HandlerMapping> matchingBeans =
					BeanFactoryUtils.beansOfTypeIncludingAncestors(context, HandlerMapping.class, true, false);
			if (!matchingBeans.isEmpty()) {
				this.handlerMappings = new ArrayList<HandlerMapping>(matchingBeans.values());
				// We keep HandlerMappings in sorted order.
				AnnotationAwareOrderComparator.sort(this.handlerMappings);
			}
		}
		else {
			try {
				HandlerMapping hm = context.getBean(HANDLER_MAPPING_BEAN_NAME, HandlerMapping.class);
				this.handlerMappings = Collections.singletonList(hm);
			}
			catch (NoSuchBeanDefinitionException ex) {
				// Ignore, we'll add a default HandlerMapping later.
			}
		}

		// Ensure we have at least one HandlerMapping, by registering
		// a default HandlerMapping if no other mappings are found.
		if (this.handlerMappings == null) {
			this.handlerMappings = getDefaultStrategies(context, HandlerMapping.class);
			if (logger.isDebugEnabled()) {
				logger.debug("No HandlerMappings found in servlet '" + getServletName() + "': using default");
			}
		}
	}
```
回想编写接口的过程， 其实就做了两件事
1. 写个方法
2. 针对请求类型，用对应的注解声明

写过自定义Java注解的一定知道，我们写了注解，并使用了注解之后，往往并没有什么用。因为程序并不认识这个注解，也不知道有这个注解的方法、类、变量需要有什么样的功能。所以我们还需要提供对应的实现，即扫描Classpath，发现如果有对应的注解声明，加上相关的实现逻辑
所以实现思路也已经很清晰了，肯定是将我们的模块伪装一下，使SpringMVC无法区分开是原生项目里面就有的方法（@RequestMapping修饰的）还是Maven依赖里的即可
于是，上面initHandlerMappings的这样一行代码给了思路
```java
Map<String, HandlerMapping> matchingBeans =
					BeanFactoryUtils.beansOfTypeIncludingAncestors(context, HandlerMapping.class, true, false);
```
所以，SpringMVC会在某个时刻，扫描ApplicationContext中所有有效的接口handler，并继续下面的逻辑

## 0x02 时间差
---
通过打断点，很意外的发现了一件事，即SpringMVC并不是在项目启动之后就有了RequestURL和Handler的映射，相反，而是在第一个请求来的时候，才会构建这样一个映射。所以存在这样一个时间差


## 0x03 操作Java字节码
---
下面，抛开技术实现，我们来想一下如何设计API：如果有这样一个基础模板，通过这个模块，我可以将我的一个逻辑包装为一个模块化的REST接口。那么我该怎么使用这个模块会舒服一些？

## 验收
---
最开始我们给的场景是天气预报，但是这里我们可以简单的测试一下我们的REST模块化方案能不能使，即先不调用天气预报的公共API，我们直接打印一行字得了。
> 注，这里我们都以SpringBoot项目为例，原始SpringMVC项目从原理上应该是可以使用的，但是使用方式可能有一些不同，具体也没有细研究

1）新建一个Maven项目，当做需要可模块化的功能接口，加上dynamic-rest-controller依赖

2）再建立一个主项目，当做我们需要集成上面接口模块的项目，并加入依赖

3) 在主项目中启动、加载模块

4) 测试接口地址

## 不足
---
这个只是自己做项目中总结的一个可以用来简化开发，进一步提升复用性的方案，其方法也比较Tweak，但是这种方式其实很大程度感谢SpringMVC初始化过程中的时间差，使Maven依赖中的模块可以成功伪装进去，但是如果不存在时间差，可能就没有这么好解决了。
下一步计划：尝试模块化Filter。场景：
> 我们知道CORS是一个跨域的方案，其主要是在后端处理，但是从实现角度本身，不同项目，只要是前后分离，经常都有跨域的问题，所以将跨域的实现抽出来也会是一个很方便的东西。但是涉及到了伪装@Filter了。操作不太一样，具体待有时间再研究吧

## Reference
---
[Demo实现源码]()
