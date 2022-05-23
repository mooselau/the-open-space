# SpringBoot General

SpringBoot 本身可以考察的内容比较多，但由于它只是 Java 成熟的框架，另外很多大公司都会基于它进行包装，所以主要还是以了解和学习为主，不建议八股文。

>推荐框架源码阅读参考 -- [芋艿源码主页](https://www.iocoder.cn/)

## Questions 一些烂大街问题

### SpringBoot 是如何启动的？

SpringBoot 的启动最开始也只是一个 SpringApplication 类，它在构造函数中就会设置一些初始值，之后会运行run() 方法来查创建 ApplicationContext 和 BeanFactory，之后会启动一个 WebServer 同时初始化这俩者，并且启动 SpringBoot中的 Bean Lifecycle，在 WebServer中 管理存活的各类 Bean。

### IoC容器 是如何完成 Bean的加载和实例化的？

在 Spring IoC 中，IoC 容器可以使用 ApplicationContext 和 BeanFactory 来描述 -- 因为所有的 Bean 最终都是需要通过他们在完成加载和实例化的，同时也会存在他们那。

Bean的加载，其实指的是 BeanDefinition的加载。BeanDefinition 是对 Bean的描述，包括 类名，scope，properties，lazyinit 等等，这些东西在 ApplicationContext 和 BeanFactory 启动过程中，使用 BeanFactoryRegistryPostProcessor 扫描项目的时候，会扫描并做成 BeanDefinition，注册到 BeanFactory 的 beanDefinitionMap 中。

Bean 的实例化，其实是在加载完 BeanDefinition 到 map 之后，在 finishBeanFactoryInitialization() 的过程中，会把所有注册进来的 beanDefinitionNames 进行逐个 getBean() 实例化和初始化，结束之后 所有的Bean就正常存活在 JVM 中了，受到 BeanFactory 和 ApplicationContext的约束和管理。

以下，会对 SpringBoot中各种概念做出分析和解释。

## IoC 容器概念

__ApplicationContext & BeanFactory__
- IoC容器本身基本上可以用这两个类来代表；
- BeanFactory 是SpringBoot中的顶层的接口，是工厂类
  + 存储了所有的 BeanDefinition和 Bean实例；
  + 同时提供了对 Bean 的操作接口，比如各种 getBean() by name/by type;
    * getBean() 作为最重要的方法之一，也是依赖注入 DI 的重要步骤，如 @Autowired
- ApplicationContext 是对 BeanFactory 进行了扩展;
  + 它在启动阶段提供的 refresh() 是IoC容器初始化的重要入口和方法；
    * 同时也是各种 Bean和 PostProcessor 的初始化和执行的阶段；
- 同时 ApplicationContext() 支持了更高级的功能，比如：
  + 消息国际化接口 MessageSource，资源模式解析接口 ResourcePatternResolver 等等

__Bean Lifecycle__

- Spring IoC 初始化 Bean的顺序：
  + 通过 @ComponentScan 或者 doScan() 实现 __Bean资源定位__，并将 BeanDefinition 注册/保存 到 beanDefinitionMap 中；
  + 在 实例化 阶段，通过 getBean() 创建 Bean 的实例对象；
  + 并通过 DI 依赖注入，如 @Autowired，将依赖实例注入到 IoC 容器中；
- Bean 的生命周期主要涉及到的接口和注解是：(按顺序排列)
  + BeanNameAware 接口的 setBeanName() 方法
  + BeanFactoryAware 接口的 setBeanFactory() 方法
  + __ApplicationContextAware__ 接口的 setApplicationContext() 方法
  + BeanPostProcessor 接口的 postProcessBeforeInitialization() 方法
    * 注意这个是对全局 Bean 生效
  + __@PostConstruct__ 自定义初始化方法
  + __InitializingBean__ 接口的 afterPropertiesSet() 方法
  + BeanPostProcessor 接口的 postProcessAfterInitialization() 方法
    * 注意这个是对全局 Bean 生效
  + Bean 正常生存期
  + __@PreDestroy__ 自定义销毁方法
  + DisposableBean 接口的 destroy() 方法
  
- 了解Bean的生命周期可以帮助，在特定情况下 统一处理 Bean的 自定义初始化和销毁的方式；

__Bean Type__

常见的 Bean 的类型有：
- Singleton, IoC 中有且仅有一个类实例；
- Prototype, 每次方法调用都会产生一个新的实例；
- Request, 每次请求都会产生一个不同的类实例；

## AOP 面向切面概念

- 简单来说，在Spring中获取到的 Bean 不是单纯的类实例，其实是一个代理类 Proxy Bean，目的之一是为了可以执行各种针对 Bean 的特殊操作，包括：
  + @Before, 
  + @After, 
  + @AfterReturning,
  + @AfterThrowing,
  + @Around

- 其中 @After 作为后置通知是 __一定会执行的__， 但是考虑到一般针对正常返回或者异常抛错会有不同的行为逻辑，建议具体具体使用 @AfterReturning 和 @AfterThrowing 替代；
- @Around 作为环绕通知，需要手动调用目标类和方法(通过JointPoint处理)，在这里可以手动对调用前和调用后进行处理（通过 try-catch 处理异常），但是考虑到 @Around 太灵活，非常容易遗漏信息甚至出错，还是建议使用分开的处理方式；

>注意的是，在 AOP 调用情况下，必须要通过外部调用，而不可以使用自我调用，否则类中的 AOP 无效。

__Dynamic Proxy__

Spring在运行过程中会根据是否是 接口类 来判断使用哪一种动态代理类：
- JDK 代理，代理类必须实现至少一个接口；
- CGLIB 代理，代理类没有实现接口；

## 常见注解

### @Transactional()

用来开启数据库事务的注解，主要作用是利用代理去除了：建立数据库连接，开启事务，提交事务，关闭连接等代码。

>更多深度分析，可以参考 [@Transactional In-Depth](https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth)

# SpringBoot In-Depth SpringBoot 深度分析

## IoC & DI 控制反转与依赖注入

IoC (Inversion of Control) 控制反转，和 DI (Dependency Inject) 依赖注入其实是紧密关联的。

IoC 主要是意味着 将对对象的控制交给框架或者容器本身来做。以前当需要创建对象的时候，会需要 new Instance()，现在通过IoC 可以让 Framework 来接管 program flow，以及进行 lib调用。如果要实现自己的行为，需要继承框架类/插件类来做。

IoC 框架由典型的特征：IoC 容器。
- Spring 中的 ApplicationContext Interface 就代表着 IoC 容器;
- Spring IoC 容器管理者 Bean的加载，配置 和 创建，以及他们的生命周期；
- ApplicationContext 有不同的几种实现，用在不同的 app场景中：
  + 比如： ClassPathXmlApplicationContext for standalone app;
  + WebApplicationContext for webapp;
  + AnnotationConfigServletWebServerApplicationContext for 注解型 web server;
- 容器一般会使用 Configuration meta data 来装配 Bean，比如 xml configuration 或者 annotations;

DI 是 实现 IoC 的方式，也是目的之一。
- 以前做依赖，会需要在 constructor中创建实例；现在只需要在 Constructor 方法上注明，让 framework 来决定传入什么实现；
- 在 spring中，主要有三种方式来注入 bean;
  + constructor based，建议用来放 mandatory 依赖；
  + setter based，建议用来放 optional 依赖；
  + @Autowired based;
    * 由于没有 construtor, setter, spring 会使用 反射来注入 bean到另一个 bean中；
    * 想比于 constructor / setter的方式，反射注入的代价会比较大;
    * 过多的 autowired了太多的 bean，反而会违背了 single responsibility princeple 的原则；
- 在 spring中，一般 IoC 都是通过 getBean by type 来找特定的 bean的；

@Autowired 默认也是使用 by type来 wire/注入的。在有多个市县的情况下要搭配 @Qualifier 来 by name 注入；

lazy-init 配置(lazy-init=true), 目的是让其在被 requested的时候才初始化，加快 application 启动的时间，但可能会隐藏 Bean 的初始化的问题。

## 从 SpringBoot 启动来看 Bean 的加载流程

SpringBoot 的主要启动入口就是 SpringApplication.run(Application.class, args);
```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
    return (new SpringApplication(primarySources)).run(args);
}
```
run 方法的主要内容就是 构建 SpringApplication 并且带着参数启动；

### SpringApplication Constructor 构造函数

如下是 SpringApplication 的构造函数：
```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
    this.sources = new LinkedHashSet();
    this.bannerMode = Mode.CONSOLE;
    this.logStartupInfo = true;
    this.addCommandLineProperties = true;
    this.addConversionService = true;
    this.headless = true;
    this.registerShutdownHook = true;
    this.additionalProfiles = new HashSet();
    this.isCustomEnvironment = false;
    this.lazyInitialization = false;
    this.resourceLoader = resourceLoader;
    Assert.notNull(primarySources, "PrimarySources must not be null");
    this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
    // 判断此 WebApp的类型 REACTIVE/SERVLET，一般是 SERVLET
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    // 读取 "META-INF/spring.factories"并筛选出 ApplicationContextInitializer 的实现类
    this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
    // 读取 "META-INF/spring.factories"并筛选出 ApplicationListener 的实现类
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    // (骚操作)通过 (new RuntimeException()).getStackTrace()，同时遍历其所有 stackTrace 来查找 main() 方法入口，进而找到 main() 所在Class并返回
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
```
这里主要是确定了 WebApplicationType，以及设置了 initializers, listeners。

>注意这里的 initializers, listeners 都是通过 spring.factories 文件读取并实例化出来的 instances，保存在了 SpringApplication 内部的 ArrayList 中。

### SpringApplication.run() 入口函数

在构造好 SpringApplication之后，会通过下方的 run() 执行主要启动流程：
```java
// 其实 run() 本身是会把 ApplicationContext 对象返回出去的
public ConfigurableApplicationContext run(String... args) {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
    this.configureHeadlessProperty();
    // 查找并初始化 ApplicationRunListener，之后全部依次启动
    SpringApplicationRunListeners listeners = this.getRunListeners(args);
    listeners.starting();

    Collection exceptionReporters;
    try {
        // 将传参 parse 并导入 ApplicationArguments
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
        this.configureIgnoreBeanInfo(environment);

        // 打印 springboot banner
        Banner printedBanner = this.printBanner(environment);
        /*
         - ⚠️ 注意这里开始 利用 反射创建 ApplicationContext, 默认使用的是 AnnotationConfigServletWebServerApplicationContext；
         - 同时也会创建 BeanFactory；
         */
        context = this.createApplicationContext();
        exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context);

        /* 
         - 设置 ConfigurableListableBeanFactory, 如 ;
         - 注册 Arguments, Banner 这两个 Singleton 到 DefaultSingletonBeanRegistry;
         */
        this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);

        /* 
          - ⚠️ 这是入口中最重要的方法，主要用来 进一步初始化 BeanFactory 和 ApplicationContext;
          - 主要的 refresh 步骤在 AbstractApplicationContext::refresh() 模版方法中；
         */
        this.refreshContext(context);

        // Nothing in afterRefresh()
        this.afterRefresh(context, applicationArguments);
        stopWatch.stop();
        if (this.logStartupInfo) {
            (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
        }

        listeners.started(context);
        this.callRunners(context, applicationArguments);
    } catch (Throwable var10) {
        this.handleRunFailure(context, var10, exceptionReporters, listeners);
        throw new IllegalStateException(var10);
    }

    try {
        listeners.running(context);
        return context;
    } catch (Throwable var9) {
        this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
        throw new IllegalStateException(var9);
    }
}
```
- 默认创建的 ApplicationContext 是 __AnnotationConfigServletWebServerApplicationContext__;
- 默认创建的 BeanFactory 是 __DefaultListableBeanFactory__;
  + Bean Factory 的类型基本上可以理解为 和 Application Context 绑定关系；
  + 在创建 AnnotationConfigServletWebServerApplicationContext 的时候，因为会 __先构造父类__ ，__再构造子类__，使得 父类(GenericApplicationContext)私有的 DefaultListableBeanFactory 得以在子类构造的时候进行创建；

>值得注意的是，在SpringBoot启动时期创建的大部分的 Bean 都是使用 BeanUtils.instantiateClass(contextClass)的方法来创建的(其内部主要是通过反射来创建)，包括 ApplicationContext 的创建。

__DefaultSingletonBeanRegistry__

- DefaultSingletonBeanRegistry 是 DefaultListableBeanFactory 的 __父类__，它有一个私有的 ConcurrentHashMap _singletonObjects_，专门用来存 单例对象，比如 环境变量，系统配置，系统环境变量等；
- _DefaultSingletonBeanRegistry::registerSingleton()_ 提供了模版方法可以用来将 singleton 对象注册到 _singletonObjects_ 中；

__AnnotationConfigServletWebServerApplicationContext 的 Type Hierachy tree__
- AnnotationConfigServletWebServerApplicationContext
- ServletWebServerApplicationContext
- GenericWebApplicationContext
- __GenericApplicationContext__
- __AbstractApplicationContext__  & __BeanDefinitionRegistry__
- ConfigurableApplicationContext 
- ApplicationContext

### AbstractApplicationContext.refresh() SpringBoot 的容器刷新函数

AbstractApplicationContext 的 refresh() 是需要重点关注的方法：
```java
public void refresh() throws BeansException, IllegalStateException {
    synchronized(this.startupShutdownMonitor) {
        // 准备工作
        this.prepareRefresh();
        ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
        /*
         - 设置 BeanFactory, 包括 BeanClassLoader, BeanPostProcessor, PropertyEditorRegistrar 等等;
         - 注册 Singleton 如 environment, systemProperties, systemEnvironment;
         */
        this.prepareBeanFactory(beanFactory);

        try {
            /* 
             - 配置 BeanPostProcessor, 如 ServletContextAwareProcessor;
             - 注册 Scopes 到 LinkedHashMap scopes 中, 如 RequestScope, SessionScope, 
             - 注册 resolvableDependency 到 ConcurrentHashMap resolvableDependencies 中, 如 ServletRequest/Response 等等;
             - 注册 EnvironmentBeans 到 SingletonBeanRegistry，如 ServLetContext 等等；
             */
            this.postProcessBeanFactory(beanFactory);

            /* 
             - ⚠️ 重要方法
             - 主要是针对 BeanDefinitionRegistryPostProcessor 和 BeanFactoryPostProcessors 进行分级, 按序执行；
             - 其中会触发 ConfigurationClassPostProcessor 的处理流程，其目的和作用是找到所有 项目中的 自定义 BeanDefinition 并导入 BeanFactory中；
             */            
            this.invokeBeanFactoryPostProcessors(beanFactory);

            /* 
             - 重点配置 BeanPostProcessor，注册为主，不执行；
             */  
            this.registerBeanPostProcessors(beanFactory);

            /* 
             - 查找或者创建 MessageSource；
             - 通过 BeanFactory 将 MessageSource 注册到 singletonObjects 中；
             */
            this.initMessageSource();
            this.initApplicationEventMulticaster();
            this.onRefresh();
            this.registerListeners();

            /* 
             - ⚠️ 重要方法
             - 主要是 BeanFactory.preInstantiateSingletons() 方法;
             */
            this.finishBeanFactoryInitialization(beanFactory);

            /* 
             - 主要是清楚缓存，初始化并启动 LifecyleProcesor;
             */
            this.finishRefresh();
        } catch (BeansException var9) {
            if (this.logger.isWarnEnabled()) {
                this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var9);
            }

            this.destroyBeans();
            this.cancelRefresh(var9);
            throw var9;
        } finally {
            this.resetCommonCaches();
        }

    }
}
```

__this.invokeBeanFactoryPostProcessors()__

invokeBeanFactoryPostProcessors() 主要流程是：
- 对传入的 list of BeanFactoryPostProcessors 进行分类处理；
  + 分类，BeanDefinitionRegistryProcessor 类型会 直接执行 并存入 registryProcessors list 中；
  + 除此以外的 类型仅存入 regularPostProcessors list 中；
- BeanFactory 从内部开始查找 特定Processor，并进行分级处理；
  + 通过 getBeanNameForType(__BeanDefinitionRegistryPostProcessor__) 获得相应类型的 PostProcessorNames;
  + 分级，PriorityOrdered 的 Processor 会先初始化(通过 getBean() 初始化)，然后执行，并存入 registryProcessors;
  + Ordered 的 Processor 继续 初始化(getBean())，然后执行，存入 registryProcessors;
  + 最后剩下的 Processor 继续 初始化和执行，存入 registryProcessors;
  + 最后，对所有 registryProcessors 和 regularPostProcessors 都执行了一遍；
    * 这里只是确保所有的 PostProcessor 都能执行一次吧
- BeanFactory 再次从内部查找 特定Processor，并进行分级处理；
  + 通过 getbeanNameForType(__BeanFactoryPostProcessor__) 获得相应类型的 PostProcessorName;
  + 分级，PriorityOrdered 的 Processor 会先初始化，然后执行；
  + Ordered 的 Processor 继续初始化，然后执行；
  + 最后 non-ordered processor 继续初始化，然后执行；

以上的 registryProcessors list 和 regularPostProcessors list 其实并没有什么作用，主要的目的还是 按顺序 初始化和执行所有的 BeanDefinitionRegistryPostProcessor 和 BeanFactoryPostProcessor;

⚠️ 需要注意的是，在 BeanDefinitionRegistryPostProcessor 中，有一个 ConfigurationClassPostProcessor 会初始化并且执行，它会找到所有本项目中的 自定义 bean，并将 beanDefinition 注册到(存入) BeanFactory的 beanDefinitionMap 中;

ConfigurationClassPostProcessor 调用链路如下:
- ConfigurationClassPostProcessor::processConfigBeanDefinition()
- ConfigurationClassparser::processConfigurationClass()
  + 这里会传入 如 demo.Application 这样的主类 bean name
- ConfigurationClassparser::doProcessConfigurationClass()
- ComponentScanAnnotationParser::parse()
- ClassPathBeanDefinitionScanner::doScan()
  + 这里会传入 base package 的信息
  + ClassPathBeanDefinitionScanner::findCandidateComponents()
  + 这里会找到所有项目下的 自定义 bean，并做成 BeanDefinition 传出

__this.registerBeanPostProcessors()__

registerBeanPostProcessors() 的主要流程是:
- BeanFactory 内部通过 getBeanNameForType(BeanPostProcessor) 获取相应 ProcessorName;
  + 分级，PriorityOrdered Processor 会先通过 getBean() 初始化，之后进入 priorityOrderedPostProcessors list;
    * 对 list中的 processor 进行注册，会保存到 (CopyOnWriteArrayList) BeanFactory.beanPostProcessors 中；
  + 之后 Ordered Processor 会继续 通过 getBean() 初始化，之后进入 orderedPostProcessors list;
    * 同样进行注册，保存到 BeanFactory.beanPostProcessors 中；
  + 最后对 non-ordered 的 processor 进行 getBean() 初始化，并存入 nonOrderedPostProcessors 中；
    * 继续注册到 BeanFactory.beanPostProcessors 中；

这里 registerBeanPostProcessors() 的作用就是把 所有的 BeanPostProcessor 放入到 AbstractBeanFactory.beanPostProcessors 中，只是单纯的注册，不会执行。但是要注意的是，BeanPostProcessor 会对所有 Bean 在生命周期中生效。

__this.finishBeanFactoryInitialization()__

finishBeanFactoryInitialization() 的主要作用就是通过 遍历 BeanFactory.beanDefinitionNames list 来逐个执行 getBean() 初始化所有的 Bean 对象，包括所有 自定义 Bean，如 常见的 UserComponent, AppConfig 等等组件。

__this.finishRefresh()__

finishRefresh() 主要是清缓存和执行 Lifecycle Processor;
- 通过 clearResourceCache() 清楚启动过程中的缓存；
- 之后通过 initLifecylceProcessor() 本地查找或者创建 DefaultLifecycleProcessor;
- 调用 LifecycleProcessor.onRefresh();
  + 通过 BeanFactory.getBeanNameForType(Lifecyle) 获得所有 Lifecyle 相关的Bean;
  + 将 Bean 转换成 Phase，并启动 phase.start();
  
所以这一步的主要目的就是 启动Lifecyle 的各个 phase。

### RegisterBean 与 RegisterSingleton

__registerSingleton()__

registerSingleton() 一般主要用来将 singleton 对象存放到 DefaultSingletonBeanRegistry.singletonObjects 中，singletonObjects 这里是 ConcurrentHashMap 的结构。

当 BeanFactory 需要查找 singleton 的时候，会通过 getSingleton() 来查找。

__registerBean()__

Bean 在创建之前，需要先注册到 BeanFactory 中。

Bean 在 register的过程中，会通过 AnnotatedBeanDefinitionReader::doRegisterBean()，做成 BeanDefinition;
- 之后 通过 BeanDefinitionReaderUtils::registerBeanDefinition() 进入 DefaultListableBeanFactory::registerBeanDefinition();
  + 之后，将 beanName(key) 和 BeanDefinition(value) 放入 DefaultListableBeanFactory::beanDefinitionMap(ConcurrentHashMap) 中；
- 在有 Bean Alias的时候，会将 alias(key)和name(value) 放到一个 aliasMap(Concurrenthashmap) 中。

在需要获取 bean 的时候，调用 getBean() 以及 doGetBean() 方法来查找或者创建bean;
- 如果创建的过程中发现依赖，会递归调用 doGetBean() 先创建依赖 Bean;

### GetBean()

getBean() 本身会调用 doGetBean() 来查找或者创建 Bean。

如果是创建 Bean的话:
- AbstractAutowireCapableBeanFactory::createBean() 会调用到 doCreateBean();
  + createBeanInstance() -> instantiateBean()，构建实例，通过 BeanUtils.instantiateClass(contextClass) 的反射的方式来构建实例;
  + populateBean(), 设置 property，会设置 PropertyValue，和一些 BeanPostProcessors;
  + initializeBean() 初始化方法调用(?)
    * BeanPostProcessor::postProcessBeforeInitialization(), Init方法调用之前的全局 BeanPostProcessor 处理, 这里包括开始对 @PostConstruct 的处理
    * invokeInitMethod() 调用特定Init方法(?)
    * BeanPostProcessor::postProcessAfterInitialization(), Init方法调用之后的全局 BeanPostProcessor 处理

当 BeanFactory 需要查找 bean的时候，可能会先通过 BeanFactory.containsLocalBean(BeanName) 来确认是否存在 Bean，这时候会主要通过 SingletonBeanRegistry 的 singletonObjects.containsKey(beanName) 和 BeanFactory BeanDefinitionMap.containsKey(beanName) 来判断的；

大致的初始化一个 bean的调用流程:
ApplicationContext::refresh()
- ApplicationContext::finishBeanFactoryInitialization()
- DefaultListableBeanFactory::preInstantiateSignletons()
  + 会遍历所有的beanDefinition，对非 lazyInit的 Bean 进行 getBean() 操作；
- DefaultListableBeanFactory::getBean()
- AbstractBeanFactory::doGetBean()
  + 在 beanDefinition 为 singleton的情况下，进行 getSingleton() 操作；
  + 在这里会执行 createBean() 操作；
  + AbstractAutowireCapableBeanFactory::doCreateBean()
  + AbstractAutowireCapableBeanFactory::createBeanInstance()
  + AbstractAutowireCapableBeanFactory::populatebean()
  + AbstractAutowireCapableBeanFactory::initializeBean()
  + AbstractAutowireCapableBeanFactory::applyBeanPostProcessorsBeforeInitialization()

## @SpringBootApplication 和自动装配

SpringBoot 的自动装配的其实更多是发生在项目启动和初始化的过程中的。
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
}
```
@SpringBootApplication 被解析发生在 Application.class 主类被扫描和解析的时候。
- @SpringBootApplication 是 springboot 项目最重要最需要了解的入口注解；
- @SpringBootApplication 主要由这些注解功能组成：@ComponentScan, @SpringBootConfiguration, @EnableAutoCOnfiguration;
- @SpringBootConfiguration 的作用是用来读取项目配置的;
- @ComponentScan 用来获取当前根目录和子目录定义的 Bean；
- @EnableAutoConfiguration 用来装载可以自动配置(autoConfiguration)的组件，而不需要通过 ComponentScan 的方式；

>Application.class 在刚创建 ApplicationContext 的时候，在被 BeanDefinitionLoader.isComponent() 判断之后，就被 registerBean() as BeanDefinition 了；
>之后，@EnableAutoConfiguration 的执行，发生在 invokeBeanFactoryPostProcessors() 的过程中，在 ConfigurationClassPostProcessor::ConfigurationClassParser.parse(Application.class) 的时候，会调用 
>
>DeferredImportSelectorGroupingHandler::processGroupImports()中，会先通过 getImports() 到逐个 AutoConfigurationImportSelector::AutoConfigurationGroup.selectImports() 完成自动装配类的收集；
>
>在之后，会回到 ConfigurationClassParser::DeferredImportSelectorGroupingHandler.processGroupImports() 流程并进入 ConfigurationClassParser::processImports() 和 ConfigurationClassParser::processConfigurationClass()，紧接着会通过 BeanUtils.instantiateClass() 完成自动装配相关 Bean 的初始化。

### @EnableAutoConfiguartion

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {}
```
这里最重要的两个注解是: @AutoConfigurationPackage 和 @Import({AutoConfigurationImportSelector.class});

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {}
```
@AutoConfigurationPackage 主要作用就是导入了 Registrar.class 类;

@Import({AutoConfigurationImportSelector.class}) 的主要作用就是导入了 AutoConfigurationImportSelector 这个类；
- AutoConfigurationImportSelector 最重要的一点就是 override了 selectImports();
  + 注意 AutoConfigurationImportSelector.selectImports() 和 AutoConfigurationGroup.selectImports();
- 将 autoconfigure 的 @Configuration Bean 返回给了调用层/IoC；

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    } else {
      /* 将 JAR 包下 "META-INF/spring-autoconfigure-metadata.properties"文件读取进来， 这个配置文件定义了所有可以 autoconfigure 的 @Configuration Bean，比如: WebMvcAutoConfiguration，以及相关配置注解等，比如 Order, ConditionalOnBean */
        AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
        
        /* 
        - 将 JAR 包下的 "META-INF/spring.factories" 文件中的 autoconfigure 配置BeanName 读取成 configuration list 进来;
        - 做好去重，利用传参 annotationMetadata 去除 exclusion；
        - 对比 configurations 和 autoConfigurationMetadata，做 filter；
        */
        AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);

        // 将筛选后的 configurations 返回；
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```
