# 设计模式-创建型


1. 从抽象程度上来讲，设计原则比设计模式更抽象。设计模式更加具体、更加可执行。
2. 大部分设计模式要解决的都是代码的可扩展性问题。
3. 了解它们都能`解决哪些问题`，掌握`典型的应用场景`，并且懂得`不过度应用`。

创建型设计模式

  1. 常用的有：单例模式、工厂模式（工厂方法和抽象工厂）、建造者模式。不常用的有：原型模式。
  2. 创建型模式主要解决对象的创建问题，封装复杂的创建过程，解耦对象的创建代码和使用代码。

## 1. 单例模式

### 1.1. 为什么使用单例模式

#### 1.1.1. 处理资源冲突（多线程环境下）

如何解决：
    1. 对象级别的锁(只能解决同一个对象在不同的线程下顺序执行，不同对象之间并不共享同一把锁)

    ```java
    public class Logger {
      private FileWriter writer;

      public Logger() {
        File file = new File("/Users/wangzheng/log.txt");
        writer = new FileWriter(file, true); //true表示追加写入(FileWriter 本身就是线程安全的，它的内部实现中本身就加了对象级别的锁)
      }

      public void log(String message) {
        synchronized(this) {
          writer.write(mesasge);
        }
      }
    }
    ```

    2. 把对象级别的锁，换成类级别的锁(可以解决问题)

    ```java
    public class Logger {
      private FileWriter writer;

      public Logger() {
        File file = new File("/Users/wangzheng/log.txt");
        writer = new FileWriter(file, true); //true表示追加写入
      }

      public void log(String message) {
        synchronized(Logger.class) { // 类级别的锁
          writer.write(mesasge);
        }
      }
    }
    ```

    3. 分布式锁（实现一个安全可靠、无 bug、高性能的分布式锁，并不是件容易的事情）
    4. 并发队列（比如 Java 中的 BlockingQueue）：多个线程同时往并发队列里写日志，一个单独的线程负责将并发队列中的数据，写入到日志文件。这种方式实现起来也稍微有点复杂。
    5. 使用单例模式（思路简单一些）：不用创建那么多 Logger 对象，一方面节省内存空间，另一方面节省系统文件句柄（对于操作系统来说，文件句柄也是一种资源，不能随便浪费）。

#### 1.1.2. 表示全局唯一类（有些数据在系统中只应保存一份）

1. 配置信息类。
2. 唯一递增 ID 号码生成器。
3. 日志类。
4. 数据库连接池类。
5. 线程池类。
6. 文件系统。

### 1.2. 如何实现一个单例

1. 构造函数需要是 private 访问权限的，这样才能避免外部通过 new 创建实例；
2. 考虑对象创建时的线程安全问题；
3. 考虑是否支持延迟加载；
4. 考虑 getInstance() 性能是否高（是否加锁）。

#### 1.2.1. 饿汉式

    ```java
    public class IdGenerator { 
      private AtomicLong id = new AtomicLong(0);
      private static final IdGenerator instance = new IdGenerator();//在类加载的时候，instance 静态实例就已经创建并初始化好了，所以，instance 实例的创建过程是线程安全的,这样的实现方式不支持延迟加载（在真正用到 IdGenerator 的时候，再创建实例）
      private IdGenerator() {}
      public static IdGenerator getInstance() {
        return instance;
      }
      public long getId() { 
        return id.incrementAndGet();
      }
    }
    // 如果初始化耗时长，那我们最好不要等到真正要用它的时候，才去执行这个耗时长的初始化过程
    // 如果实例占用资源多，按照 fail-fast 的设计原则（有问题及早暴露），那我们也希望在程序启动时就将这个实例初始化好
    ```

#### 1.2.2. 懒汉式(相对于饿汉式的优势是支持延迟加载,但是不支持高并发)

    ```java
    public class IdGenerator { 
      private AtomicLong id = new AtomicLong(0);
      private static IdGenerator instance;
      private IdGenerator() {}
      public static synchronized IdGenerator getInstance() {//getInstance() 这个方法加了一把大锁（synchronzed），导致这个函数的并发度很低。如果频繁地用到，那频繁加锁、释放锁及并发度低等问题，会导致性能瓶颈，这种实现方式就不可取了.偶尔用到可以接受
        if (instance == null) {
          instance = new IdGenerator();
        }
        return instance;
      }
      public long getId() { 
        return id.incrementAndGet();
      }
    }
    ```

#### 1.2.3. 双重检测

    ```java
    public class IdGenerator { 
      private AtomicLong id = new AtomicLong(0);
      private static IdGenerator instance;
      private IdGenerator() {}
      public static IdGenerator getInstance() {
        if (instance == null) {
          synchronized(IdGenerator.class) { // 此处为类级别的锁
            if (instance == null) {
              instance = new IdGenerator();
            }
          }
        }
        return instance;
      }
      public long getId() { 
        return id.incrementAndGet();
      }
    }
    //解决的方法很简单，只要把对象 new 操作和初始化操作设计为原子操作，就自然能禁止重排序
    ```

#### 1.2.4. 静态内部类

    ```java

    public class IdGenerator { 
      private AtomicLong id = new AtomicLong(0);
      private IdGenerator() {}

      private static class SingletonHolder{
        private static final IdGenerator instance = new IdGenerator();
      }

      public static IdGenerator getInstance() {
        return SingletonHolder.instance;
      }

      public long getId() { 
        return id.incrementAndGet();
      }
    }
    //SingletonHolder 是一个静态内部类，当外部类 IdGenerator 被加载的时候，并不会创建 SingletonHolder 实例对象。只有当调用getInstance() 方法时，SingletonHolder 才会被加载，这个时候才会创建 instance。instance 的唯一性、创建过程的线程安全性，都由JVM 来保证。所以，这种实现方法既保证了线程安全，又能做到延迟加载。
    ```

#### 1.2.5. 枚举

    ```java

    public enum IdGenerator {
      INSTANCE;
      private AtomicLong id = new AtomicLong(0);

      public long getId() { 
        return id.incrementAndGet();
      }
    }
    ```

### 1.3. 单例存在哪些问题

1. 大部分情况下，我们在项目中使用单例，都是用它来表示一些全局唯一类，比如配置信息类、连接池类、ID 生成器类。

#### 1.3.1. 单例对 OOP 特性的支持不友好

1. IdGenerator 的使用方式违背了基于接口而非实现的设计原则。
2. 从理论上来讲，单例类也可以被继承、也可以实现多态，只是实现起来会非常奇怪，会导致代码的可读性变差。

#### 1.3.2. 单例会隐藏类之间的依赖关系

1. 单例类不需要显示创建、不需要依赖参数传递，在函数中直接调用就可以了。如果代码比较复杂，这种调用关系就会非常隐蔽。

#### 1.3.3. 单例对代码的扩展性不友好

1. `单例类在某些情况下会影响代码的扩展性、灵活性。所以，数据库连接池、线程池这类的资源池，最好还是不要设计成单例类。实际上，一些开源的数据库连接池、线程池也确实没有设计成单例类`。

#### 1.3.4. 单例对代码的可测试性不友好

1. 单例类这种硬编码式的使用方式，导致无法实现 mock 替换。
2. 成员变量相当于一种全局变量。

#### 1.3.5. 单例不支持有参数的构造函数（参数只有在new instance的时候有用）

1. 第一种解决思路：在使用这个单例类的时候，要先调用 init() 方法，然后才能调用 getInstance() 方法，否则代码会抛出异常。
2. 第二种解决思路是：将参数放到 getIntance() 方法中。
3. 第三种解决思路是：将参数放到另外一个全局变量（配置对象）中。

### 1.4. 替代方案

1. 为了保证全局唯一，除了使用单例，我们还可以用`静态方法`（操作静态变量）来实现。
2. 静态方法这种实现思路，并不能解决我们之前提到的问题。实际上，它比单例更加不灵活，比如，它无法支持延迟加载。
3. 第2中方法

        ```java
        // 1. 老的使用方式
        public demofunction() {
          //...
          long id = IdGenerator.getInstance().getId();
          //...
        }

        // 2. 新的使用方式：依赖注入
        public demofunction(IdGenerator idGenerator) {
          long id = idGenerator.getId();
        }
        // 外部调用demofunction()的时候，传入idGenerator
        IdGenerator idGenerator = IdGenerator.getInsance();
        demofunction(idGenerator);
        ```

4. 类对象的全局唯一性可以通过多种不同的方式来保证。我们既可以通过单例模式来强制保证，也可以通过工厂模式、IOC 容器（比如 Spring IOC 容器）来保证，还可以通过程序员自己来保证（自己在编写代码的时候自己保证不要创建两个类对象）。

### 1.5. 如何理解单例模式中的唯一性？

1. 单例模式创建的对象是进程唯一的。在进程间是不唯一的.
2. 进程之间是不共享地址空间的，如果我们在一个进程中创建另外一个进程（比如，代码中有一个 fork() 语句，进程执行到这条语句的时候会创建一个新的进程），操作系统会给新进程分配新的地址空间，并且将老进程地址空间的所有内容，重新拷贝一份到新进程的地址空间中，这些内容包括代码、数据（比如 user 临时变量、User 对象）。
3. 线程唯一

        ```java

        public class IdGenerator {
          private AtomicLong id = new AtomicLong(0);

          private static final ConcurrentHashMap<Long, IdGenerator> instances
                  = new ConcurrentHashMap<>();

          private IdGenerator() {}

          public static IdGenerator getInstance() {
            Long currentThreadId = Thread.currentThread().getId();
            instances.putIfAbsent(currentThreadId, new IdGenerator());
            return instances.get(currentThreadId);
          }

          public long getId() {
            return id.incrementAndGet();
          }
        }
        ```

4. Java 语言本身提供了 ThreadLocal 并发工具类，可以更加轻松地实现线程唯一单例。
5. 对于 Java 语言来说，单例类对象的唯一性的作用范围并非进程，而是类加载器（Class Loader）

### 1.6. 如何实现集群环境下的单例？

1. 具体来说，我们需要把这个单例对象序列化并存储到外部共享存储区（比如文件）.进程在使用这个单例对象的时候，需要先从外部共享存储区中将它读取到内存，并反序列化成对象，然后再使用，使用完成之后还需要再存储回外部共享存储区。
2. 为了保证任何时刻，在进程间都只有一份对象存在，一个进程在获取到对象之后，需要对对象加锁，避免其他进程再将其获取。在进程使用完这个对象之后，还需要显式地将对象从内存中删除，并且释放对对象的加锁。

### 1.7. 如何实现一个多例模式？

1. “多例”指的就是，一个类可以创建多个对象，但是个数是有限制的，比如只能创建 3 个对象.多例的实现也比较简单，通过一个 Map 来存储对象类型和对象之间的对应关系，来控制对象的个数。
2. 对于多例模式，还有一种理解方式：同一类型的只能创建一个对象，不同类型的可以创建多个对象。这种多例模式的理解方式有点类似工厂模式。它跟工厂模式的不同之处是，多例模式创建的对象都是同一个类的对象，而工厂模式创建的是不同子类的对象。
3. 枚举类型也相当于多例模式，一个类型只能对应一个对象，一个类可以创建多个对象。

## 2. 工厂模式

1. 当创建逻辑比较复杂（if-else 分支判断，组合其他类对象，做各种初始化操作），是一个“大工程”的时候，我们就考虑使用工厂模式，封装对象的创建过程，将对象的创建和使用相分离。
2. 封装变化：创建逻辑有可能变化，封装成工厂类之后，创建逻辑的变更对调用者透明。代码复用：创建代码抽离到独立的工厂类之后可以复用。隔离复杂性：封装复杂的创建逻辑，调用者无需了解如何创建对象。控制复杂度：将创建代码抽离出来，让原本的函数或类职责更单一，代码更简洁。

### 2.1. 简单工厂（工厂方法模式的一种特例）

1. 根据不同的特性，创建不同的对象。

        ```java
        public class RuleConfigSource {
          public RuleConfig load(String ruleConfigFilePath) {
            String ruleConfigFileExtension = getFileExtension(ruleConfigFilePath);
            IRuleConfigParser parser = RuleConfigParserFactory.createParser(ruleConfigFileExtension);
            if (parser == null) {
              throw new InvalidRuleConfigException(
                      "Rule config file format is not supported: " + ruleConfigFilePath);
            }

            String configText = "";
            //从ruleConfigFilePath文件中读取配置文本到configText中
            RuleConfig ruleConfig = parser.parse(configText);
            return ruleConfig;
          }

          private String getFileExtension(String filePath) {
            //...解析文件名获取扩展名，比如rule.json，返回json
            return "json";
          }
        }
        // 为了让类的职责更加单一、代码更加清晰，我们还可以进一步将 createParser() 函数剥离到一个独立的类中，让这个类只负责对象的创       建。
        // 大部分工厂类都是以“Factory”这个单词结尾的，但也不是必须的
        public class RuleConfigParserFactory {
          // 工厂类中创建对象的方法一般都是 create 开头（静态的方法）
          public static IRuleConfigParser createParser(String configFormat) {
            IRuleConfigParser parser = null;
            if ("json".equalsIgnoreCase(configFormat)) {
              parser = new JsonRuleConfigParser();
            } else if ("xml".equalsIgnoreCase(configFormat)) {
              parser = new XmlRuleConfigParser();
            } else if ("yaml".equalsIgnoreCase(configFormat)) {
              parser = new YamlRuleConfigParser();
            } else if ("properties".equalsIgnoreCase(configFormat)) {
              parser = new PropertiesRuleConfigParser();
            }
            return parser;
          }
        }
        ```

2. 第二种方式，工厂模式和单例模式的结合。

        ```java
        public class RuleConfigParserFactory {
          private static final Map<String, RuleConfigParser> cachedParsers = new HashMap<>();

          static {
            cachedParsers.put("json", new JsonRuleConfigParser());
            cachedParsers.put("xml", new XmlRuleConfigParser());
            cachedParsers.put("yaml", new YamlRuleConfigParser());
            cachedParsers.put("properties", new PropertiesRuleConfigParser());
          }

          public static IRuleConfigParser createParser(String configFormat) {
            if (configFormat == null || configFormat.isEmpty()) {
              return null;//返回null还是IllegalArgumentException全凭你自己说了算
            }
            IRuleConfigParser parser = cachedParsers.get(configFormat.toLowerCase());
            return parser;
          }
        }
        ```

### 2.2. 工厂方法

1. 工厂方法模式比起简单工厂模式更加符合开闭原则。

        ```java
        public interface IRuleConfigParserFactory {
          IRuleConfigParser createParser();
        }

        public class JsonRuleConfigParserFactory implements IRuleConfigParserFactory {
          @Override
          public IRuleConfigParser createParser() {
            return new JsonRuleConfigParser();
          }
        }

        public class XmlRuleConfigParserFactory implements IRuleConfigParserFactory {
          @Override
          public IRuleConfigParser createParser() {
            return new XmlRuleConfigParser();
          }
        }

        public class YamlRuleConfigParserFactory implements IRuleConfigParserFactory {
          @Override
          public IRuleConfigParser createParser() {
            return new YamlRuleConfigParser();
          }
        }

        public class PropertiesRuleConfigParserFactory implements IRuleConfigParserFactory {
          @Override
          public IRuleConfigParser createParser() {
            return new PropertiesRuleConfigParser();
          }
        }
        ```

2. 【使用】可以为工厂类再创建一个`简单工厂`(第二种)，也就是工厂的工厂，用来创建工厂类对象。
3. 需要添加新的规则配置解析器的时候，我们只需要创建新的 parser 类和 parser factory 类，并且在 RuleConfigParserFactoryMap 类中，将新的 parser factory 对象添加到 cachedFactories 中即可。代码的改动非常少，基本上符合开闭原则。

#### 2.2.1. 使用场景

1. 当对象的创建逻辑比较复杂，不只是简单的 new 一下就可以，而是要组合其他类对象，做各种初始化操作的时候，我们推荐使用工厂方法模式，将复杂的创建逻辑拆分到多个工厂类中，让每个工厂类都不至于过于复杂。而使用简单工厂模式，将所有的创建逻辑都放到一个工厂类中，会导致这个工厂类变得很复杂。
2. 避免烦人的 if-else 分支逻辑，这个时候，我们就推荐使用工厂方法模式。
3. 【本质】`简单工厂是将不同创建逻辑放到一个工厂类中，if-else 逻辑在这个工厂类中`,`工厂方法是将不同创建逻辑放到不同工厂类中，先用一个工厂类的工厂来来得到某个工厂，再用这个工厂来创建，if-else 逻辑在工厂类的工厂中`.

### 2.3. 抽象工厂（了解）

1. 【解决的问题】在简单工厂和工厂方法中，类只有一种分类方式。如果有多种分类方法，就需要抽象工厂模式。让一个工厂负责创建多个不同类型的对象（IRuleConfigParser、ISystemConfigParser 等），而不是只创建一种 parser 对象。

```java
public interface IConfigParserFactory {
  IRuleConfigParser createRuleParser();
  ISystemConfigParser createSystemParser();
  //此处可以扩展新的parser类型，比如IBizConfigParser
}

public class JsonConfigParserFactory implements IConfigParserFactory {
  @Override
  public IRuleConfigParser createRuleParser() {
    return new JsonRuleConfigParser();
  }

  @Override
  public ISystemConfigParser createSystemParser() {
    return new JsonSystemConfigParser();
  }
}

public class XmlConfigParserFactory implements IConfigParserFactory {
  @Override
  public IRuleConfigParser createRuleParser() {
    return new XmlRuleConfigParser();
  }

  @Override
  public ISystemConfigParser createSystemParser() {
    return new XmlSystemConfigParser();
  }
}
// 省略YamlConfigParserFactory和PropertiesConfigParserFactory代码
```

### 2.4. 设计一个依赖注入框架（DI容器）

1. DI 容器底层最基本的设计思路就是基于工厂模式的。DI 容器相当于一个大的工厂类，负责在程序启动的时候，`根据配置（要创建哪些类对象，每个类对象的创建需要依赖哪些其他类对象）事先创建好对象`。当应用程序需要`使用某个类对象的时候，直接从容器中获取即可`。正是因为它持有一堆对象，所以这个框架才被称为“容器”。
2. 一个工厂类只负责某个类对象或者某一组相关类对象（继承自同一抽象类或者接口的子类）的创建，而 DI 容器负责的是整个应用中所有类对象的创建。
3. DI 容器负责的事情要比单纯的工厂模式要多。比如，它还包括配置的解析、对象生命周期的管理。
4. DI 容器在一些软件开发中已经成为了标配，比如 Spring IOC、Google Guice。但是，大部分人可能只是把它当作一个黑盒子来使用，并未真正去了解它的底层是如何实现的。当然，如果只是做一些简单的小项目，简单会用就足够了，但是，如果我们面对的是非常复杂的系统，当系统出现问题的时候，对底层原理的掌握程度，决定了我们排查问题的能力，直接影响到我们排查问题的效率。

#### 2.4.1. 配置解析

1. 将需要由 DI 容器来创建的类对象和创建类对象的必要信息（使用哪个构造函数以及对应的构造函数参数都是什么等等），放到配置文件中。容器读取配置文件，根据配置文件提供的信息来创建对象。
2. 例如spring的配置文件

```
配置文件beans.xml：
<beans>
   <bean id="rateLimiter" class="com.xzg.RateLimiter">
      <constructor-arg ref="redisCounter"/>
   </bean>
 
   <bean id="redisCounter" class="com.xzg.redisCounter">
     <constructor-arg type="String" value="127.0.0.1">
     <constructor-arg type="int" value=1234>
   </bean>
</beans>
```

#### 2.4.2. 创建对象(反射语法)

1. 只需要`将所有类对象的创建都放到一个工厂类中完成`就可以了，比如 BeansFactory。
2. 使用`反射`(一种动态加载类和创建对象的机制)机制，不管是创建一个对象还是十个对象，BeansFactory 工厂类代码都是一样的。

#### 2.4.3. 对象的生命周期管理

1. 例如spring框架
2. scope=prototype 表示返回新创建的对象，scope=singleton 表示返回单例对象。
3. lazy-init=true，对象在真正被使用到的时候（比如：BeansFactory.getBean(“userService”)）才被被创建；如果 lazy-init=false，对象在应用启动的时候就事先创建好。
4. 还可以配置对象的 init-method 和 destroy-method 方法，比如 init-method=loadProperties()，destroy-method=updateConfigFile()。

#### 2.4.4. 如何实现

#### 2.4.5. spring的循环依赖

## 3. 建造者模式（Builder）

1. 【问题】构造方法的参数过多，容易搞错各参数的顺序，传递进错误的参数值，导致非常隐蔽的 bug。
2. 可以使用set解决上边的问题，必须的参数放在构造方法中，非必须的使用set。
3. 【问题升级 合法校验（Preconditions）】必填的配置项有很多。如果我们把必填项也通过 set() 方法设置，那校验这些必填项是否已经填写的逻辑就无处安放了。
4. 【问题升级2 依赖关系（Dependencies）】假设配置项之间有一定的依赖关系。
5. 【问题升级3 不可变（Immutable）】希望 ResourcePoolConfig 类对象是`不可变对象`，也就是说，对象在创建好之后，就不能再修改内部的属性值。要实现这个功能，我们就不能在 ResourcePoolConfig 类中暴露 set() 方法。

### 3.1. 使用建造者模式

1. 把校验逻辑放置到 Builder 类中，先创建建造者，并且通过 set() 方法设置建造者的变量值，然后在使用 build() 方法真正创建对象之前，做集中的校验，校验通过之后才会创建对象。
2. ResourcePoolConfig 的构造函数改为 private 私有权限。这样我们就只能通过建造者来创建 ResourcePoolConfig 类对象。
3. ResourcePoolConfig 没有提供任何 set() 方法，这样我们创建出来的对象就是不可变对象了。

    ```java

    public class ResourcePoolConfig {
      private String name;
      private int maxTotal;
      private int maxIdle;
      private int minIdle;

      private ResourcePoolConfig(Builder builder) {
        this.name = builder.name;
        this.maxTotal = builder.maxTotal;
        this.maxIdle = builder.maxIdle;
        this.minIdle = builder.minIdle;
      }
      //...省略getter方法...

      //我们将Builder类设计成了ResourcePoolConfig的内部类。
      //我们也可以将Builder类设计成独立的非内部类ResourcePoolConfigBuilder。
      public static class Builder {
        private static final int DEFAULT_MAX_TOTAL = 8;
        private static final int DEFAULT_MAX_IDLE = 8;
        private static final int DEFAULT_MIN_IDLE = 0;

        private String name;
        private int maxTotal = DEFAULT_MAX_TOTAL;
        private int maxIdle = DEFAULT_MAX_IDLE;
        private int minIdle = DEFAULT_MIN_IDLE;

        public ResourcePoolConfig build() {
          // 校验逻辑放到这里来做，包括必填项校验、依赖关系校验、约束条件校验等
          if (StringUtils.isBlank(name)) {
            throw new IllegalArgumentException("...");
          }
          if (maxIdle > maxTotal) {
            throw new IllegalArgumentException("...");
          }
          if (minIdle > maxTotal || minIdle > maxIdle) {
            throw new IllegalArgumentException("...");
          }

          return new ResourcePoolConfig(this);
        }

        public Builder setName(String name) {
          if (StringUtils.isBlank(name)) {
            throw new IllegalArgumentException("...");
          }
          this.name = name;
          return this;
        }

        public Builder setMaxTotal(int maxTotal) {
          if (maxTotal <= 0) {
            throw new IllegalArgumentException("...");
          }
          this.maxTotal = maxTotal;
          return this;
        }

        public Builder setMaxIdle(int maxIdle) {
          if (maxIdle < 0) {
            throw new IllegalArgumentException("...");
          }
          this.maxIdle = maxIdle;
          return this;
        }

        public Builder setMinIdle(int minIdle) {
          if (minIdle < 0) {
            throw new IllegalArgumentException("...");
          }
          this.minIdle = minIdle;
          return this;
        }
      }
    }

    // 这段代码会抛出IllegalArgumentException，因为minIdle>maxIdle
    ResourcePoolConfig config = new ResourcePoolConfig.Builder()
            .setName("dbconnectionpool")
            .setMaxTotal(16)
            .setMaxIdle(10)
            .setMinIdle(12)
            .build();
    ```

4. 【缺点】如果我们并不是很关心对象是否有短暂的无效状态，也不是太在意对象是否是可变的，可以不使用此模式。另外，使用建造者模式来构建对象，代码实际上是有点重复的，ResourcePoolConfig 类中的成员变量，要在 Builder 类中重新再定义一遍。
5. 工厂模式是用来创建`不同但是相关类型`的对象（继承同一父类或者接口的一组子类），由给定的参数来决定创建哪种类型的对象。建造者模式是用来创建`一种类型的复杂对象`，通过设置不同的可选参数，“定制化”地创建不同的对象。

## 4. 原型模式

1. 【问题】如果对象的创建成本比较大，而同一个类的不同对象之间差别不大（大部分字段都相同），在这种情况下，我们可以利用对已有对象（原型）进行复制（或者叫拷贝）的方式来创建新对象，以达到节省创建时间的目的。
2. 如果对象中的数据需要经过复杂的计算才能得到（比如排序、计算哈希值），或者需要从 RPC、网络、数据库、文件系统等非常慢速的 IO 中读取，这种情况下，我们就可以利用原型模式，从其他已有对象中直接拷贝得到。
3. 我们把正在使用的数据的版本定义为“服务版本”，当我们要更新内存中的数据的时候，我们并不是直接在服务版本（假设是版本 a 数据）上更新，而是重新创建另一个版本数据（假设是版本 b 数据），等新的版本数据建好之后，再一次性地将服务版本从版本 a 切换到版本 b。这样既保证了数据一直可用，又避免了中间状态的存在。

### 4.1. 使用原型模式

1. 拷贝 currentKeywords 数据到 newKeywords 中，然后从数据库中只捞出新增或者有更新的关键词，更新到 newKeywords 中。相对于数据库的 IO 操作来说，内存操作和 CPU 计算的耗时都是可以忽略的。

### 4.2. 原型模式的实现方式：深拷贝和浅拷贝

1. 浅拷贝只会复制图中的索引（散列表），不会复制数据（SearchWord 对象）本身。相反，深拷贝不仅仅会复制索引，还会复制数据本身。
2. 在 Java 语言中，Object 类的 clone() 方法执行的就是我们刚刚说的浅拷贝。它只会拷贝对象中的基本数据类型的数据（比如，int、long），以及引用对象（SearchWord）的内存地址，不会递归地拷贝引用对象本身。
3. 【实现1】递归拷贝对象、对象的引用对象以及引用对象的引用对象……直到要拷贝的对象只包含基本数据类型数据，没有引用对象为止。
4. 【实现2】先将对象序列化，然后再反序列化成新的对象。
5. 【实现3】先采用浅拷贝的方式创建 newKeywords。对于需要更新的 SearchWord 对象，我们再使用深度拷贝的方式创建一份新的对象，替换 newKeywords 中的老对象。毕竟需要更新的数据是很少的。这种方式即利用了浅拷贝节省时间、空间的优点，又能保证 currentKeywords 中的中数据都是老版本的数据。

