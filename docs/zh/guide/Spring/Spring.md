# Spring 技术总结
本文内容参考自 [CSDN]( https://thinkwon.blog.csdn.net/article/details/104397516)

## Spring基础
1. 什么是Spring
Spring是一个轻量级的java开发框架，为了解决企业级开发的复杂度，即简化Java开发。为了简化开发，Spring采取了一下4种策略：
    * 基于POJO的轻量级和最小侵入编程
    * 通过依赖注入和面向接口实现松耦合
    * 通过切面和惯例进行声明式编程
    * 通过切面和模板减少样板代码
2. Spring有哪些模块组成
    * Spring  core：提供了框架的基本功能，包括IOC和AOP
    * Spring beans：提供了BeanFactory
    * Spring context：构建于core之上的context封装包，提供了一种框架式的对象访问方法。
    * Spring jdbc：提供了一个JDBC的抽象层，消除了烦琐的JDBC编码和数据库厂商特有的错误代码解析， 用于简化JDBC。
    * Spring aop：提供了面向切面的编程实现，让你可以自定义拦截器、切点等。
    * Spring Web：提供了针对 Web 开发的集成特性。
    * Spring test：主要为测试提供支持的，支持使用JUnit或TestNG对Spring组件进行单元测试和集成测试。
3. Spring中使用了那些设计模式
    * 工厂模式：典型的是BeanFactory
    * 单例模式：Bean默认的是单例模式
    * 代理模式：Spring aop底层使用到了JDK动态代理和CGLIB字节码生成技术
    * 模板方法：用来解决代码重复问题，例如JdbcTemplate、RestTemplate
    * 观察者模式：定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring中listener的实现ApplicationListener。
## Spring IOC
1. 什么是IOC

控制反转（IOC），它把传统上由程序代码直接操控对象的调用权交给了Spring容器去管理，通过容器来实现对象组件的装配和管理。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序本身转移到了外部容器。

2. 控制反转有什么作用
   * 管理对象的创建和维护
   * 解耦，由容器去维护具体的对象
3. IOC的优点是什么
   * 降低了代码量
   * 解耦
   * 容易测试
4. Spring IOC的实现原理是什么？

Spring中的IOC的实现原理是工厂模式加反射机制，通过反射机制找到bean，并且由工厂模式生成对应的单例。

5. Spring中单例模式是懒汉式的还是饿汉式的？
    * 饿汉模式：spring singleton的缺省是饿汉模式:启动容器时(即实例化容器时),为所有spring配置文件中定义的bean都生成一个实例
    * 懒汉模式：如果一个bean有default-lazy-init="true"这个配置的话，使用的是懒汉式

6. BeanFactory 和 ApplicationContext有什么区别？

    BeanFactory和ApplicationContext都是作为Spring的容器，ApplicationContext是BeanFactory的一个子接口。
    1. 依赖关系

        BeanFactory是Spring里边最底层的接口，包含对于bean的定义，管理bean的生命周期，读取bean的配置文件，管理bean的实例化和加载等。
        ApplicationContext接口作为BeanFactory的派生，不仅仅包括了BeanFactory的功能，还提供了完整的框架功能：
            * 支持国际化
            * 统一的资源文件访问方式
            * 提供在监听器中注册bean的事件
            * 同时加载多个配置文件
            * 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层

     2. 加载方式

         BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化。这样，我们就不能发现           一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用getBean方法才会抛出异常。
         ApplicationContext，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查         所依赖属性是否注入。 ApplicationContext启动后预载入所有的单实例Bean，通过预载入单实例bean ,确保当你需要的时候，你就不用等待，因为它们已         经创建好了。

     相对于基本的BeanFactory，ApplicationContext 唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。

     3. 创建方式

         BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader。

     4. 注册方式

         BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册         而ApplicationContext则是自动注册。
7. Applicationcontext的具体实现有那些？
    * FileSystemXmlApplicationContext：此容器从一个XML文件中加载beans的定义，xml bean配置文件中的全路径必须提供给它的构造函数。
    * ClassPathXmlApplicationContext：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置。
    * WebXmlApplicationContext：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean。

    FileSystemXmlApplicationContext和ClassPathXmlApplicationContext的区别：默认加载xml配置文件的路径不同，ClassPathXmlApplicationContext是默认从classpath下加载，FileSystemXmlApplicationContext默认是从项目根路径下加载。
8. 什么是Spring的依赖注入

    控制反转IoC是一个很大的概念，可以用不同的方式来实现。其主要实现方式有两种：依赖注入和依赖查找。
    依赖注入：相较于IoC而言，依赖注入（DI）更加准确的描述了IoC的设计理念。所谓的依赖注入，即组件之间的依赖关系由容器在应用系统运行期间决定，也就是由容器动态的将某种依赖关系的目标对象实例注入到应用系统中的各个关联组件之中。组件不做定位查询，只提供普通的Java方法让容器去决定依赖关系。
    1. 依赖注入的基本原则

    应用组件不应该负责查找资源或者其他依赖的协作对象。配置对象的工作应该由IoC容器负责，“查找资源”的逻辑应该从应用组件的代码中抽离出来，交给IoC容器负责。容器全权负责组件的装配，它会把符合依赖关系的对象通过属性或者构造器传递给需要的对象。

    2. 依赖注入的优势

    依赖注入之所以更流行是因为它是一种更可取的方式，让容器全权负责依赖查询，受管组件只需要暴露出JavaBean的setter方法或者带参数的构造器或者接口，使容器可以在初始化时组装对象的依赖关系。其与依赖查找方式相比，主要优势为：
        * 查找定位操作与应用代码无关
        * 不依赖与容器API，可以很容易地在任何容器以外使用对象
        * 不需要特殊接口，绝大多数对象可以做到不完全依赖容器

    3. 有哪些不同类型的依赖注入实现方式

       依赖注入是时下最流行的IoC实现方式，依赖注入分为接口注入（Interface Injection），Setter方法注入（Setter Injection）和构造器注入（Constructor Injection）三种方式。其中接口注入由于在灵活性和易用性比较差，现在从Spring4开始已被废弃。

       构造器依赖注入：构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。

       Setter方法注入：Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。

## Spring AOP
1. 什么是AOP

   AOP一般称为面向切面编程，作为面向对象的一种补充，用于将那些与业务无关，却与多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被称为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。可用于权限认证、日志、事务处理等。
2. AOP的相关概念

    * 切面（Aspect）： 通常是一个类，里面可以定义切入点和通知
    * 目标对象（Target）：指将要被增强的对象，即包含主业务逻辑的类对象。或者说是被一个或者多个切面所通知的对象。
    * 连接点（JoinPoint）：程序执行过程中的一个点，如方法的执行或异常的处理。在SpringAOP中，连接点总是表示方法的执行。通俗讲，连接点表示类里边可以被增强的方法。
    * 切入点（PointCut）：切入点是与连接点匹配的表达式，用于确定是否需要执行通知。切入点使用与连接点匹配的不同类型的表达式，Spring框架中使用AspectJ切入点表达式语言，我们可以理解为切入点是需要被拦截的连接点。
    * 增强/通知（Advice）：所谓通知就是切入点被拦截后做的事情，通知分为前置通知、后置通知、异常通知、最终通知和环绕通知(切面要完成的功能)
    * 织入（weave）:将切面应用到目标上并导致对象创建的过程
    * 引入（introduction）：在不修改代码的前提下，引入可以在运行期为类动态的添加一些方法或字段
3. Advice通知类型
    * Before:在目标方法被调用之前做增强处理,@Before只需要指定切入点表达式即可
    * AfterReturning:在目标方法正常完成后做增强,@AfterReturning除了指定切入点表达式后，还可以指定一个返回值形参名returning,代表目标方法的返回值
    * AfterThrowing:主要用来处理程序中未处理的异常,@AfterThrowing除了指定切入点表达式后，还可以指定一个throwing的返回值形参名,可以通过该形参名来访问目标方法中所抛出的异常对象
    * After:在目标方法完成之后做增强，无论目标方法时候成功完成。@After可以指定一个切入点表达式
    * Around:环绕通知,在目标方法完成前后做增强处理,环绕通知是最重要的通知类型,像事务,日志等都是环绕通知,注意编程中核心是一个ProceedingJoinPoint
4. Spring AOP 和AspectJ AOP有什么区别？AOP有哪些实现方式？

  AOP实现的关键在于代理模式，AOP代理主要分为静态代理和动态代理。静态代理的代表为AspectJ，动态代理则以Spring AOP为代表。
   * AspectJ是静态代理的增强，所谓的静态代理是指AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，他会在编译阶段将AspectJ（切面）织入到Java字节码中，运行的时候就是增强之后的AOP对象。
* Spring AOP使用的是动态代理，所谓的动态代理就是AOP框架不会去修改字节码，而是每次运行时在内存临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。
5. JDK动态代理和CGLIB动态代理的区别

  Spring AOP中的动态代理主要有两种方式：JDK动态代理和CGLIB动态代理

   * JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。

   * 如果代理类没有实现 InvocationHandler 接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。

6. spring 如何决定使用jdk动态代理和cglib
   1. 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP
   2. 如果目标对象实现了接口，可以强制使用CGLIB实现AOP
   3. 如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换

    如何强制使用CGLIB实现AOP？
    1. 添加CGLIB库，SPRING_HOME/cglib/*.jar
    2. 在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>

    JDK动态代理和CGLIB字节码生成的区别？
     1. JDK动态代理只能对实现了接口的类生成代理，而不能针对类
     2. CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法

    因为是继承，所以该类或方法最好不要声明成final

## Spring Beans
1. 什么是Spring beans

    Spring Beans是那些形成Spring应用的主干的Java对象。它们被Sprin IOC容器初始化，装配和管理。这些beans通过容器配置的元数据创建，比如，以XML文件中的形式定义。
2. 一个Spring Bean定义包含什么？

    一个Spring Bean的定义包含容器必知的所有配置元数据，包括如何创建一个bean，它的生命周期详情以及它的依赖。
3. 如何给Spring容器提供配置元数据？Spring有几种配置方式

     Spring有三种给容器提供配置元数据的方式：
     * XML配置文件
     * 基于注解配置
     * 基于java的配置
4. Spring基于xml注入bean的几种方式
    1. set方法注入
    2. 构造器注入：①通过index设置参数的位置 ②通过type设置参数类型
    3. 静态工厂注入
    4. 实例工厂注入
5. Spring bean的作用域有哪些

    Spring框架支持一下五种bean作用域：
    * singelton：bean在每个Spring IoC容器中只有一个实例
    * prototype：一个bean可以用多个实例
    * request：每次http请求都会创建一个bean，该作用域仅在基于web的Spring ApplicationContext情形下有效
    * session：在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
    * global-session：在一个全局的HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。

     **注意**： 缺省的Spring bean 的作用域是Singleton。使用 prototype 作用域需要慎重的思考，因为频繁创建和销毁 bean 会带来很大的性能开销。
6. Spring框架中单例的bean是线程安全的吗？

     不是，Spring框架中的单例bean不是线程安全的,Spring中的bean默认是单例模式，Spring框架并没有对单例bean进行多线程的封装处理。

     实际上大部分时候 spring bean 无状态的（比如 dao 类），所有某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。
     * 有状态就是有数据存储功能。
     * 无状态就是不会保存数据。
 7. Spring如何处理多线程并发问题

    在一般情况下，只有无状态的bean才可以在多线程环境下共享，在Spring中，绝大部分的bean都可以声明为singleton作用域，因为Spring对一些bean中非线程安全状态采用ThreadLocal进行处理，解决线程安全问题。

8. 理解Spring中Bean的生命周期
   1. Spring对bean实例化
   2. Spring将值和bean的引用注入到bean对应的属性中
   3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBeanName
   4. 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入
   5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法,将bean所在的应用上下文的引用传入进来
   6. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessBeforeInitilization()方法
   7. 如果bean实现了InitializingBean接口，Spring将调用它们的afterPropertiesSet()方法。类似的，如果bean使用init-method声明了初始化方法，该方法也会调用
   8. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁
   9. 如果bean实现了DisposableBean接口，Spring将调用它的destory()接口方法。同样，如果bean使用destory-method声明了销毁方法，该方法也会被调用
9. 哪些是重要的bean生命周期的方法？你能重载它们吗？

    有两个重要的bean生命周期的方法，第一个是setUp，它是在容器加载bean的时候调用。第二个方法是teardown它是在容器卸载类的时候被调用。
        bean标签有两个重要的属性（init-method和destory-method）。用它们可以定制初始化和注销方法。它们也有相应的注解（@PostConstruct和@PreDestroy）
10. 什么是Spring的内部bean？什么是Spring inner beans？

     在Spring框架中，当一个bean仅被用作另一个bean的属性时，它能被声明为一个内部bean。内部bean可以用setter注入“属性”和构造方法注入“构造参数”的方式来实现，内部bean通常是匿名的，它们的Scope一般是prototype。
11. Spring有那些自动装配bean的方式

    * no：默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean。
    * byName：通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自动装配。
    * byType：通过参数的数据类型进行自动装配。
    * constructor：利用构造函数进行装配，并且构造函数的参数通过byType进行装配。
    * autodetect：自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配。
12. 使用@Autowried注解自动装配的过程是怎样的？

    在启动Spring IoC时，容器自动装载了一个AutowiredAnnotationBeanPostProcessor后置处理器，当容器扫描到@Autowied、@Resource或@Inject时，就会在IoC容器自动查找需要的bean，并装配给该对象的属性。在使用@Autowired时，首先在容器中查询对应类型的bean：
    * 如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据；
    * 如果查询的结果不止一个，那么@Autowired会根据名称来查找；
    * 如果上述查找的结果为空，那么会抛出异常。解决方法时，使用required=false。

## Spring事务
1. 说一下Spring的事务传播行为

spring事务的传播行为说的是，当多个事务同时存在的时候，spring如何处理这些事务的行为。

* PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。
* PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。
* PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。
* PROPAGATION_REQUIRES_NEW：创建新事务，无论当前存不存在事务，都创建新事务。
* PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
*  PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
*  PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

2. 说一下 spring 的事务隔离？

spring 有五大隔离级别，默认值为 ISOLATION_DEFAULT（使用数据库的设置），其他四个隔离级别和数据库的隔离级别一致：

* ISOLATION_DEFAULT：用底层数据库的设置隔离级别，数据库设置的是什么我就用什么；
* ISOLATION_READ_UNCOMMITTED：未提交读，最低隔离级别、事务未提交前，就可被其他事务读取（会出现幻读、脏读、不可重复读）；
* ISOLATION_READ_COMMITTED：提交读，一个事务提交后才能被其他事务读取到（会造成幻读、不可重复读），SQL server 的默认级别；
* ISOLATION_REPEATABLE_READ：可重复读，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致，禁止读取到别的事务未提交的数据（会造成幻读），MySQL 的默认级别；
* ISOLATION_SERIALIZABLE：序列化，代价最高最可靠的隔离级别，该隔离级别能防止脏读、不可重复读、幻读。
