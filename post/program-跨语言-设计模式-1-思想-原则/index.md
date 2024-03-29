# 设计模式-思想-原则


摘自《极客时间x王争|设计模式之美》

## 1. 解决的问题

### 1.1. 提高代码质量

1. 解决命名不规范、类设计不合理、分层不清晰、没有模块化概念、代码结构混乱、高度耦合等等问题。

### 1.2. 提高复杂代码的设计和开发能力

1. 如何分层、分模块？应该怎么划分类？每个类应该具有哪些属性、方法？怎么设计类之间的交互？该用继承还是组合？该使用接口还是抽象类？怎样做到解耦、高内聚低耦合？该用单例模式还是静态方法？用工厂模式创建对象还是直接 new 出来？如何避免引入设计模式提高扩展性的同时带来的降低可读性问题？等等问题。

### 1.3. 提高阅读源码的能力

### 1.4. 提高Leader相关能力

## 2. 评价代码质量

1. 代码质量的评价具有很强的主观性。

### 2.1. 可维护性*

1. 对于一个项目来说，维护代码的时间远远大于编写代码的时间。
2. 在不破坏原有代码设计、不引入新的 bug 的情况下，`能够快速地修改或者添加代码`(可读性、可扩展性)。

### 2.2. 可读性*

1. Any fool can write code that a computer can understand. Good programmers write code that humans can understand.
2. `编码规范`尤其重要。
3. 代码是否符合编码规范、命名是否达意、注释是否详尽、函数是否长短合适、模块划分是否清晰、是否符合高内聚低耦合等等.
4. `code review`是一个很好的测验代码可读性的手段。

### 2.3. 可扩展性*

1. 在不修改或少量修改原有代码的情况下，通过扩展的方式`添加新的功能代码`。
2. 代码预留了一些功能扩展点，你可以把新功能代码，直接插到扩展点上，而不需要因为要添加一个功能而大动干戈，改动大量的原始代码。
3. 涉及到后边的设计原则`对修改关闭，对扩展开放`.

### 2.4. 灵活性

1. `易扩展`也可以说灵活性好。
2. 原有代码中，已经抽象出了很多底层可以复用的模块、类等代码，我们可以拿来直接使用。`易复用`。
3. 接口易用。

### 2.5. 简洁性（简单、复杂）

1. `思从深而行从简`，真正的高手能云淡风轻地用最简单的方法解决最复杂的问题。这也是一个编程老手跟编程新手的本质区别之一。
2. KISS.

### 2.6. 可复用性

1. 当讲到面向对象特性的时候，我们会讲到继承、多态存在的目的之一，就是为了提高代码的可复用性；
2. 当讲到设计原则的时候，我们会讲到单一职责原则也跟代码的可复用性相关；
3. 当讲到重构技巧的时候，我们会讲到解耦、高内聚、模块化等都能提高代码的可复用性。
4. DRY

### 2.7. 可测试性

1. 代码的可测试性差，比较难写单元测试，那基本上就能说明代码设计得有问题。

## 3. 方法论

### 3.1. 面向对象（思想）

1. 面向对象编程因为其具有丰富的特性（封装、抽象、继承、多态），可以实现很多复杂的设计思路，是很多设计原则、设计模式编码实现的基础。
2. 面向对象编程是一种编程范式或编程风格。它`以类或对象作为组织代码的基本单元`，并将封装、抽象、继承、多态四个特性，作为代码设计和实现的基石 。
3. 面向对象编程语言是支持类或对象的语法机制，并有现成的语法机制，能方便地实现面向对象编程四大特性（封装、抽象、继承、多态）的编程语言。

#### 3.1.1. 面向对象的四大特性：封装、抽象、继承、多态

##### 3.1.1.1. 封装

1. 封装也叫作信息隐藏或者数据访问保护。类通过暴露有限的访问接口，授权外部仅能通过类提供的方式（或者叫函数）来访问内部信息或者数据。
2. 对于封装这个特性，我们需要`编程语言本身提供一定的语法机制来支持`。`这个语法机制就是访问权限控制`。
3. 【解决问题】提高可读性，防止误操作。
4. 【解决问题】将属性封装起来，暴露少许的几个必要的方法给调用者使用，调用者就不需要了解太多背后的业务细节，用错的概率就减少很多。

##### 3.1.1.2. 抽象

1. 如何隐藏方法的具体实现，让调用者`只需要关心方法提供了哪些功能`，并不需要知道这些功能是如何实现的。
2. 常借助编程语言提供的接口类（比如 Java 中的 interface 关键字语法）或者抽象类（比如 Java 中的 abstract 关键字语法）这两种语法机制，来实现抽象这一特性。
3. 类的方法是通过编程语言中的“函数”这一语法机制来实现的。`通过函数包裹具体的实现逻辑，这本身就是一种抽象`。调用者在使用函数的时候，并不需要去研究函数内部的实现逻辑，只需要通过函数的命名、注释或者文档，了解其提供了什么功能，就可以直接使用了。
4. 【意义】而抽象作为一种只关注功能点不关注实现的设计思路，正好帮我们的大脑过滤掉许多非必要的信息。
5. 【解决问题】在定义（或者叫命名）类的方法的时候，也要有抽象思维，`不要在方法定义中，暴露太多的实现细节`，以保证在某个时间点需要改变方法的实现逻辑的时候，不用去修改其定义。

##### 3.1.1.3. 继承

1. is-a.
2. `单继承`表示一个子类只继承一个父类，`多继承`表示一个子类可以继承多个父类，
3. 有些编程语言只支持单继承，不支持多重继承，比如 Java、PHP、C#、Ruby 等，而有些编程语言既支持单重继承，也支持多重继承，比如 C++、Python、Perl 等。
4. 【意义】代码复用。
5. 【意义】反应真实世界中的关系，非常符合人类的认知。
6. 【但是】过度使用继承，继承层次过深过复杂，就会导致代码可读性、可维护性变差。

##### 3.1.1.4. 多态

1. 多态是指，子类可以替换父类，在实际的代码运行过程中，调用子类的方法实现。
2. 多态这种特性也需要编程语言提供特殊的语法机制来实现。

    ```shell
    第一个语法机制是编程语言要支持父类对象可以引用子类对象，也就是可以将 SortedDynamicArray 传递给 DynamicArray。
    第二个语法机制是编程语言要支持继承，也就是 SortedDynamicArray 继承了 DynamicArray，才能将 SortedDyamicArray 传递给    DynamicArray。
    第三个语法机制是编程语言要支持子类可以重写（override）父类中的方法，也就是 SortedDyamicArray 重写了 DynamicArray 中的 add   () 方法。
    ```

3. 除了利用“继承加方法重写”这种实现方式之外，我们还有其他两种比较常见的的实现方式，一个是利用`接口类语法`，另一个是利用 `duck-typing 语法`[两个对象有相同的方法]。不过，并不是每种编程语言都支持接口类或者 duck-typing 这两种语法机制，比如 C++ 就不支持接口类语法，而 duck-typing 只有一些动态语言才支持，比如 Python、JavaScript 等。
4. 【duck-typing】只要两个类具有相同的方法，就可以实现多态，并不要求两个类之间有任何关系，这就是所谓的 duck-typing，是一些动态语言所特有的语法机制。而像 Java 这样的静态语言，通过继承实现多态特性，必须要求两个类之间有继承关系，通过接口实现多态特性，类必须实现对应的接口。
5. 【意义】多态特性能提高代码的可扩展性和复用性。

#### 3.1.2. 面向对象编程与面向过程编程的区别和联系

1. 面向过程编程也是一种编程范式或编程风格。它以过程（可以理解为方法、函数、操作）作为组织代码的基本单元，以`数据（可以理解为成员变量、属性）与方法相分离`为最主要的特点。面向过程风格是一种流程化的编程风格，通过拼接一组顺序执行的方法来操作数据完成一项功能。
2. OOP 更加能够应对大规模复杂程序的开发.
3. OOP 风格的代码更易复用、易扩展、易维护。

```shell
1. 基于多态这个特性，我们在需要修改一个功能实现的时候，可以通过实现一个新的子类的方式，在子类中重写原来的功能逻辑，用子类替换父类。在实际的代码运行过程中，调用子类新的功能逻辑，而不是在原有代码上做修改。这就遵从了“对修改关闭、对扩展开放”的设计原则，提高代码的扩展性。
2. 除此之外，利用多态特性，不同的类对象可以传递给相同的方法，执行不同的代码逻辑，提高了代码的复用性。
```

#### 3.1.3. 面向对象分析、面向对象设计、面向对象编程（哪些代码设计看似是面向对象，实际是面向过程的）

1. 围绕着对象或类来做需求分析和设计.
2. 分析和设计两个阶段最终的产出是类的设计，包括程序被拆解为哪些类，每个类有哪些属性方法，类与类之间如何交互等等。
3. 面向对象分析就是要搞清楚做什么，面向对象设计就是要搞清楚怎么做，面向对象编程就是将分析和设计的的结果翻译成代码的过程.
4. 不管使用面向过程还是面向对象哪种风格来写代码，我们最终的目的还是写出易维护、易读、易复用、易扩展的高质量代码。

##### 3.1.3.1. 滥用 getter、setter 方法

1. 提供了 public 的 getter、setter 方法，这就跟将这两个属性定义为 public 公有属性，没有什么两样了。
2. 违反了面向对象编程的封装特性（通过访问权限控制，隐藏内部数据，外部仅能通过类提供的有限的接口访问、修改内部数据）。
3. 在设计实现类的时候，除非真的需要，否则，尽量不要给属性定义 setter 方法。
4. 除此之外，尽管 getter 方法相对 setter 方法要安全些，但是如果返回的是集合容器（比如例子中的 List 容器），也要防范集合内部数据被修改的危险。

##### 3.1.3.2. 滥用全局变量和全局方法

1. 在面向对象编程中，常见的全局变量有单例类对象（单例类对象在全局代码中只有一份它相当于一个全局变量）、静态成员变量（被所有的实例化对象所共享）、常量（一些代码中的配置参数，一般都设置为常量，放到一个 `Constants` 类）等，常见的全局方法有静态方法（一般用来操作静态变量或者外部数据，各种 `Utils `类，里面的方法一般都会定义成静态方法）。
2. 定义一个如此大而全的 Constants 类，并不是一种很好的设计思路.

    ```shell
    1. 在开发过程中，可能都要涉及修改这个类,影响代码的可维护性。
    2. 增加代码的编译时间(单元测试)。
    3. 影响代码的复用性。
    ```

3. 【优化】`将 Constants 类拆解为功能更加单一的多个类`；并不单独地设计 Constants 常量类，而是哪个类用到了某个常量，我们就把这个常量定义到这个类中。
4. 只包含静态方法不包含任何属性的 Utils 类，是彻彻底底的面向过程的编程风格。
5. 【优化】确实有必要去定义这样一个 Utils 类，那就大胆地去定义它；`针对不同的功能，设计不同的 Utils 类`。

##### 3.1.3.3. 定义数据和方法分离的类

1. 传统的 MVC 结构分为 Model 层、Controller 层、View 层这三层。不过，在做前后端分离之后，三层结构在后端开发中，会稍微有些调整，被分为 Controller 层、Service 层、Repository 层。Controller 层负责暴露接口给前端调用，Service 层负责核心业务逻辑，Repository 层负责数据读写。而在每一层中，我们又会定义相应的 VO（View Object）、BO（Business Object）、Entity。一般情况下，VO、BO、Entity 中只会定义数据，不会定义方法，所有操作这些数据的业务逻辑都定义在对应的 Controller 类、Service 类、Repository 类中。这就是典型的面向过程的编程风格。
2. 这种开发模式叫作`基于贫血模型的开发模式`，也是我们现在非常常用的一种 Web 项目的开发模式。

#### 3.1.4. 接口和抽象类的区别以及各自的应用场景

1. 从类的继承层次上来看，抽象类是一种自下而上的设计思路，先有子类的代码重复，然后再抽象成上层的父类（也就是抽象类）。而接口正好相反，它是一种自上而下的设计思路。我们在编程的时候，一般都是先设计接口，再去考虑具体的实现。

##### 3.1.4.1. 抽象类

1. 抽象类不允许被实例化，只能被继承。
2. 抽象类可以包含属性和方法。方法既可以包含代码实现，也可以不包含代码实现。不包含代码实现的方法叫作抽象方法。
3. 子类继承抽象类，必须实现抽象类中的所有抽象方法。
4. 抽象类实际上就是类，只不过是一种特殊的类，这种类不能被实例化为对象，只能被子类继承。（is-A）

###### 3.1.4.1.1. 存在的意义

1. 代码复用。
2. 【如果用普通类】达到多态必须要有方法定义，但是方法有子类实现。如果父类定义一个空的方法，影响可读性；其次子类会忘记重写；可以被实例化，会被误用。`所以要有抽象类`。

##### 3.1.4.2. 接口

1. 接口不能包含属性（也就是成员变量）。
2. 接口只能声明方法，方法不能包含代码实现。
3. 类实现接口的时候，必须实现接口中声明的所有方法。
4. 接口可以表示一种（has-A）功能的关系。（`功能协议`）

###### 3.1.4.2.1. 意义

1. 更侧重于解耦。对行为的一种抽象，相当于一组`协议或者契约`。
2. 调用者只需要关注抽象的接口，不需要了解具体的实现，具体的实现代码对调用者透明。接口实现了约定和实现相分离，可以降低代码间的耦合性，提高代码的可扩展性。

    ```java
    // 普通类模拟接口
    public class MockInteface {
      protected MockInteface() {}//避免这个类被实例化
      public void funcA() {
        throw new MethodUnSupportedException();//强迫子类在继承这个父类的时候，都去主动实现父类的方法，否则就会在运行时抛出异常
      }
    }
    ```

3. 【其他不支持抽象类和接口的语言（python），可以使用普通类模拟抽象类和接口,或使用duck-typing】。

#### 3.1.5. 【基于接口而非实现编程的设计思想】

1. 应用这条原则，可以`将接口和实现相分离，封装不稳定的实现，暴露稳定的接口`。`上游系统面向接口而非实现编程`，不依赖不稳定的实现细节，这样当实现发生变化的时候，上游系统的代码基本上不需要做改动，以此来降低耦合性，提高扩展性。
2. 在软件开发中，最大的挑战之一就是需求的不断变化，这也是考验代码设计好坏的一个标准。
3. 越抽象、越顶层、越脱离具体某一实现的设计，越能提高代码的灵活性，越能应对未来的需求变化。好的代码设计，不仅能应对当下的需求，而且在将来需求发生变化的时候，仍然能够在不破坏原有代码设计的情况下灵活应对。
4. “细节是魔鬼”。这句话在软件开发中特别适用。
5. “基于接口而非实现编程”这条原则，不仅仅可以指导非常细节的编程开发，还能指导更加上层的架构设计、系统设计等。比如，服务端与客户端之间的“接口”设计、类库的“接口”设计。

##### 3.1.5.1. 具体的做法

1. 函数的命名不能暴露任何实现细节。
2. 封装具体的实现细节。
3. 为实现类定义抽象的接口。具体的实现类都依赖统一的接口定义，遵从一致的功能协议。使用者依赖接口，而不是具体的实现类来编程。
4. 【注意】在定义接口的时候，不要暴露任何实现细节。接口的定义只表明做什么，而不是怎么做。要多思考一下，这样的接口设计是否足够通用，是否能够做到在替换具体的接口实现的时候，不需要任何接口定义的改动。
5. 如果在我们的业务场景中，某个功能只有一种实现方式，未来也不可能被其他实现方式替换，那我们就没有必要为其设计接口，也没有必要基于接口编程，直接使用实现类就可以了。

#### 3.1.6. 【多用组合少用继承的设计思想】

##### 3.1.6.1. 继承的问题和可替代

1. 组合优于继承，多用组合少用继承。
2. 【问题】继承最大的问题就在于：继承层次过深、继承关系过于复杂会影响到代码的可读性和可维护性。（鸟类的例子[会不会飞，会不会下蛋，会不会叫]）
3. 我们可以利用组合（composition）、接口、委托（delegation）三个技术手段，一块儿来解决刚刚继承存在的问题。

```java
第一步：使用接口实现方法的声明。【由于接口没有实现，会造成代码重复】
第二步：定义接口对应的类，来实现接口。
第三步：具体的类通过组合和委托技术来消除代码重复。

public interface Flyable {
  void fly()；
}
public class FlyAbility implements Flyable {
  @Override
  public void fly() { //... }
}
//省略Tweetable/TweetAbility/EggLayable/EggLayAbility

public class Ostrich implements Tweetable, EggLayable {//鸵鸟
  private TweetAbility tweetAbility = new TweetAbility(); //组合
  private EggLayAbility eggLayAbility = new EggLayAbility(); //组合
  //... 省略其他属性和方法...
  @Override
  public void tweet() {
    tweetAbility.tweet(); // 委托
  }
  @Override
  public void layEgg() {
    eggLayAbility.layEgg(); // 委托
  }
}
```

4. 继承主要有三个作用：表示 is-a 关系，支持多态特性，代码复用。而这三个作用都可以通过其他技术手段来达成。比如 is-a 关系，我们可以通过组合和接口的 has-a 关系来替代；多态特性我们可以利用接口来实现；代码复用我们可以通过组合和委托来实现。所以，从理论上讲，通过组合、接口、委托三个技术手段，我们完全可以替换掉继承，在项目中不用或者少用继承关系，特别是一些复杂的继承关系。

##### 3.1.6.2. 选择继承还是组合

1. 【问题】组合的问题：要做更细粒度的类的拆分。这也就意味着，我们要定义更多的类和接口。
2. 如果类之间的继承`结构稳定（不会轻易改变），继承层次比较浅（比如，最多有两层继承关系），继承关系不复杂`，我们就可以大胆地使用继承。反之，系统越不稳定，继承层次很深，继承关系复杂，我们就尽量使用组合来替代继承。
3. 有一些设计模式会固定使用继承或者组合。
4. 如果你不能改变一个函数的入参类型，而入参又非接口(是类)，为了支持多态，只能采用继承来实现。

#### 3.1.7. 面向过程的贫血模型和面向对象的充血模型

##### 3.1.7.1. 贫血模式

1. MVC三层架构（数据（有一个数据类只有数据）和方法分离）（Repository 层负责数据访问，Service 层负责业务逻辑，Controller 层负责暴露接口）。

##### 3.1.7.2. 充血模式：领域驱动设计（DDD）

1. 数据和对应的业务逻辑被封装到同一个类中。
2. 跟基于贫血模型的传统开发模式的`区别主要在 Service 层`（Service 层包含 Service 类和 Domain 类两部分）。
3. 基于贫血模型的传统的开发模式，比较适合业务比较简单的系统开发。相对应的，基于充血模型的 DDD 开发模式，更适合业务复杂的系统开发。比如，包含各种利息计算模型、还款模型等复杂业务的金融系统。
4. 【两种不同的开发模式会导致不同的开发流程。】SQL 驱动（SQL-Driven）的开发模式；和需要事先理清楚所有的业务，定义领域模型所包含的属性和方法。领域模型相当于可复用的`业务中间层`。新功能需求的开发，都基于之前定义好的这些领域模型来完成；
5. 而基于充血模型的 DDD 开发模式，正好需要我们`前期做大量的业务调研、领域模型设计`，所以它更加适合这种复杂系统的开发。

##### 3.1.7.3. 实战（虚拟钱包）要点

###### 3.1.7.3.1. 功能

1. 充值：第一个操作是从用户的银行卡账户转账到`应用的公共银行卡账户`；第二个操作是将用户的充值金额加到虚拟钱包余额上；第三个操作是记录刚刚这笔交易流水。
2. 支付：从用户的虚拟钱包账户划钱到商家的虚拟钱包账户上。除此之外，我们也需要记录这笔支付的交易流水信息。
3. 提现：扣减用户虚拟钱包中的余额，并且触发真正的银行转账操作，从应用的公共银行账户转钱到用户的银行账户。同样，我们也需要记录这笔提现的交易流水信息。
4. 查询余额和交易流水。
5. 虚拟钱包系统和三方支付系统。

###### 3.1.7.3.2. 实现

1. 【最终一致性】对于支付这样的类似转账的操作，我们在操作两个钱包账户余额之前，先记录交易流水，并且标记为“待执行”，当两个钱包的加减金额都完成之后，我们再回过头来，将交易流水标记为“成功”。在给两个钱包加减金额的过程中，如果有任意一个操作失败，我们就将交易记录的状态标记为“失败”。我们通过后台补漏 Job，拉取状态为“失败”或者长时间处于“待执行”状态的交易记录，重新执行或者人工介入处理。
2. 虚拟钱包系统不应该感知具体的业务交易类型。通过记录`两条交易流水信息的方式`来解决。整个钱包系统分为两个子系统，`上层钱包系统的实现，依赖底层虚拟钱包系统和三方支付系统`。对于钱包系统来说，它可以感知充值、支付、提现等业务概念，所以，我们在钱包系统这一层额外再记录一条包含交易类型的交易流水信息，而在底层的虚拟钱包系统中记录不包含交易类型的交易流水信息。

###### 3.1.7.3.3. 在基于充血模型的 DDD 开发模式中，将业务逻辑移动到 Domain 中，Service 类变得很薄

1. Service 类负责与 Repository 交流。保持领域模型的独立性，不与任何其他层的代码（Repository 层的代码）或开发框架（比如 Spring、MyBatis）耦合在一起，将流程性的代码逻辑（比如从 DB 中取数据、映射数据）与领域模型的业务逻辑解耦，让领域模型更加可复用。
2. Service 类负责跨领域模型的业务聚合功能。(转账，后期成长为转账领域模型)
3. Service 类负责一些非功能性及与三方系统交互的工作。

###### 3.1.7.3.4. 在基于充血模型的 DDD 开发模式中，尽管 Service 层被改造成了充血模型，但是 Controller 层和 Repository 层还是贫血模型

1. 没有必要改造。（业务逻辑简单）
2. Repository 的 Entity 来说，即便它被设计成贫血模型，违反面向对象编程的封装特性，有被任意代码修改数据的风险，但 Entity 的生命周期是有限的。一般来讲，我们把它传递到 Service 层之后，就会转化成 BO 或者 Domain 来继续后面的业务逻辑。Entity 的生命周期到此就结束了，所以也并不会被到处任意修改。
3. Controller 层的 VO。实际上 VO 是一种 DTO（Data Transfer Object，数据传输对象）。它主要是作为接口的数据传输承载体，将数据发送给其他系统。从功能上来讲，它理应不包含业务逻辑、只包含数据。

#### 3.1.8. 实战(接口鉴权)

1. 针对框架、组件、类库等非业务系统的开发，我们一定要有组件化意识、框架意识、抽象意识，开发出来的东西要足够通用，不能局限于单一的某个业务需求。

##### 3.1.8.1. 面向对象分析

1. 先从最简单的方案想起，然后再优化（提出问题，解决问题）。

```
1. 第一轮：通过用户名加密码来做认证（最简单）。重放攻击（截获用户名和密码）。
2. 第二轮：将请求接口的 URL 跟 AppID、密码拼接在一起，然后进行加密，生成一个 token。将这个 token 及 AppID，随 URL 一块传递给微服务端。仍然存在重放攻击的风险，还是不够安全。
3. 第三轮：优化 token 生成算法，引入一个随机变量，让每次接口请求生成的 token 都不一样。微服务端在收到这些数据之后，会验证当前时间戳跟传递过来的时间戳，是否在一定的时间窗口内（比如一分钟）。如果超过一分钟，则判定 token 过期，拒绝接口请求。如果没有超过一分钟，则说明 token 没有过期，就再通过同样的 token 生成算法，在服务端生成新的 token，与调用方传递过来的 token 比对。还是可以有1分钟重放风险。权衡安全性、开发成本、对系统性能的影响，这个方案算是比较折中、比较合理的了。
4. 第四轮：针对 AppID 和密码的存储，我们最好能灵活地支持各种不同的存储方式，比如 ZooKeeper、本地配置文件、自研配置中心、MySQL、Redis 等。
```

2. 需求定义是否清晰、合理，直接影响了后续的设计、编码实现是否顺畅。

##### 3.1.8.2. 面向对象设计

1. 面向对象分析的产出是详细的需求描述，那面向对象设计的产出就是`类`。在面向对象设计环节，我们将需求描述转化为`具体的类的设计`。
```
划分职责进而识别出有哪些类；
定义类及其属性和方法；
定义类与类之间的交互关系；
将类组装起来并提供执行入口。
```

###### 3.1.8.2.1. 划分职责进而识别出有哪些类
2. 类是现实世界中事物的一个建模。但是，并不是每个需求都能映射到现实世界，也`并不是每个类(抽象的概念)都与现实世界中的事物一一对应`。
3. 【方法】`把需求描述中的名词罗列出来，作为可能的候选类，然后再进行筛选`。
4. 【另外一种方法】那就是根据需求描述，把其中涉及的`功能点（动词）(尽可能的小，只做一件很小的事情，单一职责)，一个一个罗列出来`，然后再去看哪些功能点`职责相近，操作同样的属性`，可否应该归为同一个类。
5. 【演进】基于粗糙的类划分，进行迭代。
6. 【嵌套】针对复杂的需求开发，我们首先要做的是进行模块划分，将需求先简单划分成几个小的、独立的功能模块，然后再在模块内部，应用刚刚的方法，进行面向对象设计。而模块的划分和识别，跟类的划分和识别，是类似的套路。

###### 3.1.8.2.2. 定义类及其属性和方法
1. 需求描述中的动词选为方法，需求描述中的名词选为属性。
2. 结合业务模型，筛选合适的属性和方法，或者增加一些属性和方法。

###### 3.1.8.2.3. 定义类与类之间的交互关系
1. UML 统一建模语言中定义了六种类之间的关系。
```
**泛化（Generalization）可以简单理解为继承关系
**实现（Realization）一般是指接口和实现类之间的关系。
聚合（Aggregation）是一种包含关系:A 类对象包含 B 类对象，B 类对象的生命周期可以不依赖 A 类对象的生命周期(外来的依赖注入)，也就是说可以单独销毁 A 类对象而不影响 B 对象.
**组合（Composition）也是一种包含关系。A 类对象包含 B 类对象，B 类对象的生命周期跟依赖 A 类对象的生命周期，B 类对象不可单独存在，比如鸟与翅膀之间的关系。
关联（Association）是一种非常弱的关系，包含聚合、组合两种关系
**依赖（Dependency）是一种比关联关系更加弱的关系，包含关联关系
```

###### 3.1.8.2.4. 将类组装起来并提供执行入口
1. 封装成一个入口。

##### 3.1.8.3. 面向对象编程

##### 3.1.8.4. 灵活应用

### 3.2. 设计原则

1. 对于每一种设计原则，我们需要掌握它的设计初衷，能`解决哪些编程问题`，`有哪些应用场景`。
2. 是其他的指导原则。

#### 3.2.1. SOLID 原则 -SRP 单一职责原则

##### 3.2.1.1. 如何理解
1. 一个`类`或者`模块(多个类组成一个模块)`只负责完成一个职责（或者功能）。
2. 实际使用中，拆分是模糊的，需要结合具体的应用场景和角度。（用户展示信息，用户认证信息，用户物流信息）
3. 我们可以先写一个粗粒度的类，满足业务需求。随着业务的发展，如果粗粒度的类越来越庞大，代码越来越多，这个时候，我们就可以将这个粗粒度的类，拆分成几个更细粒度的类。这就是所谓的`持续重构`。

##### 3.2.1.2. 如何判定
1. 类中的代码行数(200)、函数或属性过多(10)，会影响代码的可读性和可维护性，我们就需要考虑对类进行拆分；
2. 类`依赖的其他类`过多，或者依赖类的其他类过多，不符合高内聚、低耦合的设计思想，我们就需要考虑对类进行拆分；
3. `私有方法过多`，我们就要考虑能否将私有方法独立到新的类中，设置为 public 方法，供更多的类使用，从而提高代码的复用性；
4. `比较难给类起一个合适名字`，很难用一个业务名词概括，或者只能用一些笼统的 Manager、Context 之类的词语来命名，这就说明类的职责定义得可能不够清晰；
5. 类中大量的方法都是集中操作类中的某几个属性.

##### 3.2.1.3. 防止过渡使用
1. 如果拆分得过细，实际上会适得其反，反倒会降低内聚性，也会影响代码的可维护性。

#### 3.2.2. SOLID 原则 -OCP 开闭原则（对扩展开放，对修改关闭）
1. 这条原则最有用，那是因为，扩展性是代码质量最重要的衡量标准之一。在 23 种经典设计模式中，大部分设计模式都是为了解决代码的扩展性问题而存在的，主要遵从的设计原则就是开闭原则。

##### 3.2.2.1. 如何理解
1. 软件实体（模块、类、方法等）应该“对扩展开放、对修改关闭”。添加一个新的功能应该是，在已有代码基础上扩展代码（新增模块、类、方法等），而非修改已有代码（修改模块、类、方法等）。
2. 只要它没有破坏原有的代码的正常运行，没有破坏原有的单元测试，我们就可以说，这是一个合格的代码改动。
3. 尽量让修改操作更集中、更少、`更上层`，尽量让最核心、最复杂的那部分逻辑代码满足开闭原则。
4. 以最小的修改代码的代价来完成新功能的开发。

##### 3.2.2.2. 如何做到
1. 偏向顶层的指导思想。为了尽量写出扩展性好的代码，我们要时刻具备扩展意识、抽象意识、封装意识。这些“潜意识”可能比任何开发技巧都重要。
2. 在写代码的时候后，我们要`多花点时间往前多思考一下，这段代码未来可能有哪些需求变更、如何设计代码结构，事先留好扩展点`，以便在未来需求变更的时候。
3. 在识别出代码`可变部分和不可变部分`之后，我们要将可变部分封装起来，隔离变化，提供抽象化的不可变接口，给上层系统使用。

##### 3.2.2.3. 具体方法
1. 利用多态、依赖注入、基于接口而非实现编程以及大部分的设计模式，来实现“对扩展开放、对修改关闭”。
2. 对于一些不确定未来是否要支持的需求，或者实现起来比较复杂的扩展点，我们可以等到有需求驱动的时候，再通过重构代码的方式来支持扩展的需求。
3. 在扩展性和可读性之间做权衡。在某些场景下，代码的扩展性很重要，我们就可以适当地牺牲一些代码的可读性；在另一些场景下，代码的可读性更加重要，那我们就适当地牺牲一些代码的可扩展性。

#### 3.2.3. SOLID 原则 -LSP 里式替换原则

1. 子类对象（object of subtype/derived class）能够替换程序（program）中父类对象（object of base/parent class）出现的任何地方，并且保证原来程序的逻辑行为（behavior）不变及正确性不被破坏。
2. 【验证的窍门】拿父类的单元测试去验证子类的代码。如果某些单元测试运行失败，就有可能说明，子类的设计实现没有完全地遵守父类的约定，子类有可能违背了里式替换原则。
3. 一般情况下，我们写的代码都不怎么会违背它。
4. 里式替换原则是用来指导，`继承关系中子类该如何设计`的一个原则。

##### 3.2.3.1. 多态和里氏替换原则的区别

1. `多态是面向对象编程的一大特性`，也是面向对象编程语言的一种语法。它是一种代码实现的思路。而里式替换是一种设计原则，是用来`指导继承关系中子类该如何设计的`，子类的设计要保证在替换父类的时候，不改变原有程序的逻辑以及不破坏原有程序的正确性。
2. 【按照协议来设计】子类在设计的时候，要遵守父类的行为约定（或者叫协议）。父类定义了函数的行为约定，那子类可以改变函数的内部实现逻辑，但不能改变函数原有的行为约定。这里的行为约定包括：`函数声明要实现的功能；对输入、输出、异常的约定；甚至包括注释中所罗列的任何特殊说明`。

##### 3.2.3.2. 违反的例子

1. 子类违背父类声明要实现的功能；
2. 子类违背父类对输入、输出、异常的约定；
3. 子类违背父类注释中所罗列的任何特殊说明。

#### 3.2.4. SOLID 原则 -ISP 接口隔离原则

1. 客户端`不应该被强迫依赖它不需要的接口`。其中的“客户端”，可以理解为接口的调用者或者使用者。
2. 理解接口隔离原则的关键，就是理解其中的“接口”二字.

##### 3.2.4.1. 把“接口”理解为一组 API 接口集合

1. 在设计微服务或者类库接口的时候，如果部分接口只被部分调用者使用，那我们就需要将这部分接口隔离出来，单独给对应的调用者使用，而不是强迫其他调用者也依赖这部分不会被用到的接口。

##### 3.2.4.2. 把“接口”理解为单个 API 接口或函数

1. 接口隔离原则就可以理解为：函数的设计要功能单一，不要将多个不同的功能逻辑在一个函数中实现.
2. 单一职责原则针对的是模块、类、接口的设计。而接口隔离原则相对于单一职责原则，一方面它更侧重于接口的设计，另一方面它的思考的角度不同。它提供了一种`判断接口是否职责单一的标准`：通过调用者如何使用接口来间接地判定。如果调用者只使用部分接口或接口的部分功能，那接口的设计就不够职责单一。
3. 部分调用者只需要函数中的部分功能，那我们就需要把函数拆分成粒度更细的多个函数，让调用者只依赖它需要的那个细粒度函数。

##### 3.2.4.3. 把“接口”理解为 OOP 中的接口概念

1. 那接口的设计要尽量单一，不要让接口的实现类和调用者，依赖不需要的接口函数。

#### 3.2.5. SOLID 原则 -DIP 依赖倒置(依赖反转)原则

##### 3.2.5.1. 控制反转（IOC）

1. 自己写main函数和使用框架（在框架预留的扩展点，填充具体的代码就可以了，完全不需要写负责执行流程的 main() 函数），典型的通过框架实现控制反转。
2. 这里的“控制”指的是对程序执行流程的控制，而“反转”指的是在没有使用框架之前，程序员自己控制整个程序的执行。在使用框架之后，整个程序的执行流程可以通过框架来控制。流程的控制权从程序员“反转”到了框架。
3. 控制反转并不是一种具体的实现技巧，而是一个`比较笼统的设计思想`，一般用来指导框架层面的设计。

##### 3.2.5.2. 依赖注入（DI）

1. 是一种具体的编码技巧。
2. 用一句话来概括就是：不通过 new() 的方式在类内部创建依赖类对象，而是将依赖的类对象在外部创建好之后，通过构造函数、函数参数等方式传递（或注入）给类使用。
3. 通过依赖注入的方式来将依赖的类对象传递进来，这样就提高了代码的扩展性，我们可以灵活地替换依赖的类。`“开闭原则”`。当然，还有继续优化的空间，依赖的类定义成接口，`基于接口而非实现编程`。
4. 编写可测试性代码最有效的手段。

##### 3.2.5.3. 依赖注入框架（DI Framework）

1. 而对象创建和依赖注入的工作，本身跟具体的业务无关，我们完全可以抽象成框架(依赖注入框架)来自动完成。
2. 只需要通过`依赖注入框架提供的扩展点`，简单配置一下所有需要创建的类对象、类与类之间的依赖关系，就可以实现由框架来自动创建对象、管理对象的生命周期、依赖注入等原本需要程序员来做的事情。
3. 现成的依赖注入框架有很多，比如 Google Guice、Java Spring、Pico Container、Butterfly Container 等。Spring 框架自己声称是控制反转容器（Inversion Of Control Container）。Spring 框架的控制反转主要是通过依赖注入来实现的。不过这点区分并不是很明显，也不是很重要。

##### 3.2.5.4. 依赖反转原则（DIP）

1. 高层模块（high-level modules）不要依赖低层模块（low-level）。高层模块和低层模块应该通过抽象（abstractions）来互相依赖。除此之外，抽象（abstractions）不要依赖具体实现细节（details），具体实现细节（details）依赖抽象（abstractions）。
2. 在调用链上，调用者属于高层，被调用者属于低层。
3. 这条原则主要还是用来`指导框架层面的设计`，跟前面讲到的控制反转类似。
4. Tomcat 和应用程序代码之间并没有直接的依赖关系，两者都依赖同一个“抽象”，也就是 Servlet 规范.

#### 3.2.6. KISS 原则、YAGNI 原则

##### 3.2.6.1. KISS
1. 尽量保持简单。
2. 一般来说，工具类的功能都比较通用和全面，所以，在代码实现上，需要考虑和处理更多的细节，执行效率就会有所影响。
3. 本身就复杂的问题，用复杂的方法解决，并不违背 KISS 原则。
3. 【方法】不要使用同事可能不懂的技术来实现代码。比如前面例子中的正则表达式，还有一些编程语言中过于高级的语法等。
4. 【方法】不要重复造轮子，要善于使用已经有的工具类库。经验证明，自己去实现这些类库，出 bug 的概率会更高，维护的成本也比较高。
5. 【方法】不要过度优化。不要过度使用一些奇技淫巧（比如，位运算代替算术运算、复杂的条件语句代替 if-else、使用一些过于底层的函数等）来优化代码，牺牲代码的可读性。
6. 【判断】code review。

##### 3.2.6.2. YAGNI

1. 不要去设计当前用不到的功能；不要去编写当前用不到的代码。实际上，这条原则的核心思想就是：不要做过度设计。但是预留好扩展点。
2. 不要在项目中提前引入不需要依赖的开发包。

##### 3.2.6.3. 小插曲
1. 用于校验和匹配的正则表达式使用时需要预先compile，在类中写做静态变量（经常会重用），这样可以提高效率。Pattern.compile(IP_REGEXP)。

#### 3.2.7. DRY

1. 不要写重复的代码。
2. 重复的代码不一定违反 DRY 原则，而且有些看似不重复的代码也有可能违反 DRY 原则。
3. 【实现逻辑重复】尽管代码的实现逻辑是相同的，但语义不同，我们判定它并不违反 DRY 原则，满足“单一职责原则”和“接口隔离原则”。
4. 【功能语义重复】实现逻辑不同，但功能是相同的，违反。
5. 【代码执行重复】违反。

##### 3.2.7.1. 代码复用性

1. `代码复用`表示一种行为：我们在开发新功能的时候，尽量复用已经存在的代码。`代码的可复用性`表示一段代码可被复用的特性或能力：我们在编写代码的时候，让代码尽量可复用。DRY 原则是一条原则：不要写重复的代码。
2. 复用、可复用性、DRY 原则这三者从理解上有所区别，但实际上要达到的目的都是类似的，都是为了减少代码量，提高代码的可读性、可维护性。

##### 3.2.7.2. 如何提高复用性

1. 减少代码耦合；
2. 满足单一职责原则；
3. 模块化：不单单指一组类构成的模块，还可以理解为单个类、函数。
4. 业务与非业务逻辑分离：越是跟业务无关的代码越是容易复用，越是针对特定业务的代码越难复用。
5. 通用代码下沉。从分层的角度来看，越底层的代码越通用、会被越多的模块调用，越应该设计得足够可复用。一般情况下，在代码分层之后，为了避免交叉调用导致调用关系混乱，我们只允许上层代码调用下层代码及同层代码之间的调用，杜绝下层代码调用上层代码。
6. 继承、多态、抽象、封装：利用继承，可以将公共的代码抽取到父类，子类复用父类的属性和方法。利用多态，我们可以动态地替换一段代码的部分逻辑，让这段代码可复用。
7. 应用模板等设计模式。`模板模式`利用了多态来实现，可以灵活地替换其中的部分代码，整个流程模板代码可复用。
8.【其他】泛型编程等.`复用意识`：在写代码的时候，我们要多去思考一下，这个部分代码是否可以抽取出来，作为一个独立的模块、类或者函数供多处使用。在设计每个模块、类、函数的时候，要像设计一个外部 API 那样，去思考它的复用性。
9. [Rule of Three]第一次编写代码的时候，我们不考虑复用性；第二次遇到复用场景的时候，再进行重构使其复用。

#### 3.2.8. LOD（迪米特法则）

1. 利用这个原则，能够帮我们实现代码的“高内聚、松耦合”。

##### 3.2.8.1. 何为“高内聚、松耦合”

1. “高内聚、松耦合”是一个比较通用的设计思想，可以用来指导不同粒度代码的设计与开发，比如系统、模块、类，甚至是函数，也可以应用到不同的开发场景中，比如微服务、框架、组件、类库等。
2. 【类】“高内聚”用来指导类本身的设计，“松耦合”用来指导类与类之间依赖关系的设计。
3. 所谓高内聚，就是指相近的功能应该放到同一个类中，不相近的功能不要放到同一个类中。单一职责原则是实现代码高内聚非常有效的设计原则。
4. 所谓松耦合是说，在代码中，类与类之间的依赖关系简单清晰。即使两个类有依赖关系，一个类的代码改动不会或者很少导致依赖类的代码改动。实际上，我们前面讲的依赖注入、接口隔离、基于接口而非实现编程，以及迪米特法则，都是为了实现代码的松耦合。

##### 3.2.8.2. “迪米特法则”

1. 最小知识原则，英文翻译为：The Least Knowledge Principle。
2. 每个模块（unit）只应该了解那些与它关系密切的模块（units: only units “closely” related to the current unit）的有限知识（knowledge）。或者说，每个模块只和自己的朋友“说话”（talk），不和陌生人“说话”（talk）。
3. 不该有`直接依赖关系`的类之间，不要有依赖；有依赖关系的类之间，尽量只依赖必要的接口（也就是定义中的“有限知识”）。
```
1. 底层网络通信类,不应该依赖具体的请求内容。
2. 构造函数中的 downloader.downloadHtml() 逻辑复杂，耗时长，不应该放到构造函数中，会影响代码的可测试性。
3. 构造函数要使用依赖注入，不应该使用new。
```
4. `将方法都抽象细化成接口。依赖注入接口编程`。“基于最小接口而非最大实现编程”

#### 3.2.9. 业务实战
1. 一个工程师来说，如果要追求长远发展，你就不能一直只把自己放在执行者的角色，不能只是一个代码实现者，你还要有独立负责一个系统的能力，能端到端（end to end）开发一个完整的系统。这其中的工作就包括：前期的需求沟通分析、中期的代码设计实现、后期的系统上线维护等。
2. 技术人员应该更多地参与到产品设计中。具备产品思维。

##### 3.2.9.1. 需求分析
1. 创造的一大秘诀是要懂得如何隐藏你的来源。`借鉴`
2. 产品的线框图、用户用例（user case ）或者叫用户故事（user story）来细化业务流程，挖掘一些比较细节的、不容易想到的功能点。
3. `用户故事`模拟用户如何使用我们的产品，描述用户在一个特定的应用场景里的一个完整的业务操作流程。

##### 3.2.9.2. 系统设计
1. 类似面向对象设计。
```
1. 合理地将功能划分到不同模块:如果一个功能的修改或添加，经常要跨团队、跨项目、跨系统才能完成，那说明模块划分的不够合理，职责不够清晰，耦合过于严重;分层（纵向）。
2. 设计模块与模块之间的交互关系：一种是同步接口调用，另一种是利用消息中间件异步调用。第一种方式简单直接，第二种方式的解耦效果更好。上下层系统之间的调用倾向于通过同步接口，同层之间的调用倾向于异步消息调用。
3. 设计模块的接口、数据库、业务模型
```

##### 3.2.9.3. 代码实现
1. 在设计接口和数据库的时候，一定要多花点心思和时间，切不可过于随意。相反，业务逻辑代码侧重内部实现，不涉及被外部依赖的接口，也不包含持久化的数据，所以对改动的容忍性更大。
2. 接口设计要符合单一职责原则，粒度越小通用性就越好。为了兼顾易用性和性能，我们可以借鉴 facade（外观）设计模式，在职责单一的细粒度接口之上，再封装一层粗粒度的接口给外部使用。

###### 3.2.9.3.1. 为什么要分MVC三层开发
1. 分层能起到代码复用的作用；
2. 分层能起到隔离变化的作用；分层体现了一种抽象和封装的设计思想。
3. 分层能起到隔离关注点的作用：三层之间的关注点不同，分层之后，职责分明，更加符合单一职责原则，代码的内聚性更好。
4. 分层能提高代码的可测试性：`下层通过依赖注入的方式供上层测试`。
5. 分层能应对系统的复杂性:拆分有垂直和水平两个方向。水平方向基于业务来做拆分，就是模块化；垂直方向基于流程来做拆分，就是这里说的分层。

###### 3.2.9.3.2. BO、VO、Entity 存在的意义是什么？
1. VO、BO、Entity 并非完全一样。
2. VO、BO、Entity 三个类虽然代码重复，但功能语义不重复，从职责上讲是不一样的。
3. 为了尽量减少每层之间的耦合，把职责边界划分明确，每层都会维护自己的数据对象，层与层之间通过接口交互。
4. 既然 VO、BO、Entity 不能合并，那如何解决代码重复的问题呢？`继承和组合`。
5. 代码重复问题解决了，那不同分层之间的数据对象该如何互相转化呢？Java 中提供了多种数据对象转化工具，比如 BeanUtils、Dozer 等，可以大大简化繁琐的对象转化工作。

#### 3.2.10. 非业务实战（接口调用的各种统计信息）

##### 3.2.10.1. 功能性需求分析

##### 3.2.10.2. 非功能性需求分析
1. 易用性
2. 性能：低延时，低消耗。
3. 扩展性：特指使用者可以在不修改框架源码，甚至不拿到框架源码的情况下，为框架扩展新的功能。类似给框架开发插件（`依赖注入，面向接口非实现`）。
4. 容错性:要对框架可能存在的各种异常情况都考虑全面，对外暴露的接口抛出的所有运行时、非运行时异常都进行捕获处理。
5. 通用性:除了接口统计这样一个需求，还可以适用到其他哪些场景中.

##### 3.2.10.3. 框架设计
1. 对于稍微复杂系统的开发，借鉴 TDD（测试驱动开发）和 Prototype（最小原型）的思想，先聚焦于一个简单的应用场景，基于此设计实现一个简单的原型。尽管这个最小原型系统在功能和非功能特性上都不完善，但它能够看得见、摸得着，比较具体、不抽象，能够很有效地帮助我缕清更复杂的设计思路，是迭代设计的基础。
2. 侵入式和非侵入性（AOP）。参考[Spring(4)——面向切面编程（AOP模块）](https://www.jianshu.com/p/994027425b44)

##### 3.2.10.4. 实现
1. 小步快跑，逐步迭代。
2. 在实际的软件开发中，这两个过程往往是交叉进行的。一般是先有一个粗糙的设计，然后着手实现，实现的过程发现问题，再回过头来补充修改设计。
3. 至于到底选择哪种划分方法，判定的标准是让代码尽量地满足低耦合、高内聚、单一职责、对扩展开放对修改关闭等之前讲的各种设计原则和思想，尽量地做到代码可复用、易读、易扩展、易维护。

### 3.3. 重构技巧

1. 设计思想、设计原则、设计模式一个最重要的应用场景就是在重构的时候。重构作为保持代码质量不下降的有效手段，利用的就是面向对象、设计原则、设计模式、编码规范这些理论。
2. 在`开发初期，除非特别必须，我们一定不要过度设计，应用复杂的设计模式`。而是当代码`出现问题的时候，我们再针对问题，应用原则和模式进行重构`。这样就能有效避免前期的过度设计。
3. 重构代码对一个工程师能力的要求，要比单纯写代码高得多。重构需要你能`洞察出代码存在的坏味道或者设计上的不足`，并且能合理、熟练地利用设计思想、原则、模式、编程规范等理论知识解决这些问题。

#### 3.3.1. 重构的目的（why）、对象（what）、时机（when）、方法（how）
1. Martin Fowler:重构是一种对软件内部结构的改善，目的是在`不改变软件的可见行为`的情况下，使其更易理解，修改成本更低。
2. 在保持功能不变的前提下，利用设计思想、原则、模式、编程规范等理论来优化代码，修改设计上的不足，提高代码质量。

##### 3.3.1.1. WHY
3. 首先，重构是时刻保证代码质量的一个极其有效的手段，不至于让代码腐化到无可救药的地步。
4. 其次，优秀的代码或架构不是一开始就能完全设计好的，就像优秀的公司和产品也都是迭代出来的。重构代码也是不可避免的。
5. 最后，重构是避免过度设计的有效手段。在我们维护代码的过程中，真正遇到问题的时候，再对代码进行重构，能有效避免前期投入太多时间做过度的设计，做到有的放矢。
6. 重构对一个工程师本身技术的成长也有重要的意义。重构非常锻炼一个人的代码能力。
7. 初级工程师在维护代码，高级工程师在设计代码，资深工程师在重构代码.

##### 3.3.1.2. WHAT
1. 笼统地分为大规模高层次重构（以下简称为“大型重构”）和小规模低层次的重构（以下简称为“小型重构”）。
2. 高层次重构:对顶层代码设计的重构，包括：`系统、模块、代码结构、类与类之间的关系`等的重构，重构的手段有：分层、模块化、解耦、抽象可复用组件等等。
3. 小型重构指的是对代码细节的重构，主要是针对`类、函数、变量`等代码级别的重构，比如规范命名、规范注释、消除超大类或函数、提取重复代码等等。小型重构更多的是利用我们能后面要讲到的`编码规范`。

##### 3.3.1.3. WHEN
1. 一条可持续、可演进的方式`持续重构`.
2. 时刻具有持续重构意识.(没事儿的时候重构、顺手重构)

##### 3.3.1.4. HOW
1. 在进行大型重构的时候，我们要提前做好完善的`重构计划`，有条不紊地`分阶段(一天)`来进行。每个阶段完成一小部分代码的重构，然后提交、测试、运行，发现没有问题之后，再继续进行下一阶段的重构，保证代码仓库中的代码`一直处于可运行、逻辑正确`的状态。
2. 大规模高层次的重构一定是有组织、有计划，并且非常谨慎的，需要有经验、熟悉业务的资深同事来主导。
3. 小重构，随时。除了人工去发现低层次的质量问题，我们还可以借助很多成熟的静态代码分析工具（比如 CheckStyle、FindBugs、PMD），来自动发现代码中的问题，然后针对性地进行重构优化。

#### 3.3.2. 保证重构不出错的技术手段：单元测试和代码的可测试性
1. 【如何保证重构不出错】熟练掌握各种设计原则、思想、模式，还需要对所重构的业务和代码有足够的了解。除了这些个人能力因素之外，最可落地执行、最有效的保证重构不出错的手段应该就是单元测试（Unit Testing）。

##### 3.3.2.1. 什么是单元测试？
1. 单元测试由研发工程师自己来编写，用来测试自己写的代码的正确性。
2. 集成测试的测试对象是整个系统或者某个功能模块。单元测试的测试对象是类或者函数（代码层级）。
3. 更多的是考验程序员思维的缜密程度，看能否设计出覆盖各种正常及异常情况的测试用例，来保证代码在任何预期或非预期的情况下都能正确运行。

##### 3.3.2.2. 为什么要写单元测试？
1. 单元测试除了能有效地为重构保驾护航之外，也是保证代码质量最有效的两个手段之一（另一个是 Code Review）。
2. 单元测试能有效地帮你发现代码中的 bug。坚持写单元测试是保证代码质量的一个“杀手锏”，也是帮助拉开与其他人差距的一个“小秘密”。
3. 单元测试能帮你发现代码设计上的问题。对于一段代码，如果`很难为其编写单元测试`，或者单元测试写起来很吃力，需要依靠`单元测试框架`里很高级的特性才能完成，那往往就意味着代码设计得不够合理，比如，没有使用依赖注入、大量使用静态函数、全局变量、代码高度耦合等。
4. 单元测试是对集成测试的有力补充:`利用mock 的方式，控制 mock 的对象返回我们需要模拟的异常，来测试代码在这些异常情况的表现`。
5. 写单元测试的过程本身就是代码重构的过程.
6. 阅读单元测试能帮助你快速熟悉代码.`程序员都不怎么喜欢写文档和注释，而大部分程序员写的代码又很难做到“不言自明”。`
7. 单元测试是 TDD 可落地执行的改进方案:测试用例先于代码编写很难执行；先写代码，紧接着写单元测试，更加容易被执行，又兼顾了TDD的优点。

##### 3.3.2.3. 如何编写单元测试？
1. 写单元测试就是针对代码设计覆盖各种输入、异常、边界条件的测试用例，并将这些测试用例翻译成代码的过程。
2. 在把测试用例翻译成代码的时候，我们可以利用`单元测试框架`，来简化测试代码的编写。比如，Java 中比较出名的单元测试框架有 Junit、TestNG、Spring Test 等。
3. 不耗时；质量可以放低一些；
4. 很多现成的工具专门用来做覆盖率统计，比如，JaCoCo、Cobertura、Emma、Clover。覆盖率的计算方式有很多种，比较简单的是语句覆盖，稍微高级点的有：条件覆盖、判定覆盖、路径覆盖。将覆盖率作为衡量单元测试质量的唯一标准是不合理的。
5. 从过往的经验上来讲，一个项目的单元测试覆盖率在 60～70% 即可上线。如果项目对代码质量要求比较高，可以适当提高单元测试覆盖率的要求。
6. 单元测试不要依赖被测试函数的具体实现逻辑，它只关心被测函数实现了什么功能。
7. 写单元测试本身不需要太复杂的技术，`大部分单元测试框架都能满足`(不满足肯定代码测试性不好)。在公司内部，起码团队内部需要统一单元测试框架。

##### 3.3.2.4. 如何在团队中推行单元测试？
1. 首先要保证新写的代码都要有单元测试，其次，每次在改动到某个类时，如果没有单元测试就顺便补上，不过这要求工程师们有足够强的`主人翁意识`（ownership），毕竟光靠 leader 督促，很多事情是很难执行到位的。

#### 3.3.3. 代码的可测试性
1. 写出可测试的代码反倒是件非常有挑战的事情。

##### 3.3.3.1. 什么是代码的可测试性？
1. 代码的可测试性可以从侧面上反应代码设计是否合理。
2. 所谓代码的可测试性，就是针对代码编写单元测试的难易程度。

##### 3.3.3.2. 如何写出可测试的代码？
1. 【注】如果代码中依赖了外部系统或者不可控组件，比如，需要依赖数据库、网络通信、文件系统等，那我们就需要将被测代码与外部系统解依赖，而这种解依赖的方法就叫作“mock”。所谓的 mock 就是用一个“假”的服务替换真正的服务。mock 的服务完全在我们的控制之下，模拟输出我们想要的数据。
2. 【注】mock 的方式主要有两种，手动 mock 和利用框架 mock。利用框架 mock 仅仅是为了简化代码编写，每个框架的 mock 方式都不大一样。
3. 手动实现mock,非单例类即继承原有类，编写一个子类。通过`依赖注入`。单例类，可以创建一个中间类隔离一下，来间接调用单例类。
4. 【注】代码中包含跟“时间”有关的“未决行为”逻辑。一般的处理方式是将这种未决行为逻辑重新封装。将未决行为逻辑重新封装为一个方法（然后使用匿名内部类的方式，重写这个方法）。

##### 3.3.3.3. 有哪些常见的不好测试的代码（Anti-Patterns反模式）
1. 代码中包含未决行为。所谓的未决行为逻辑就是，代码的输出是随机或者说不确定的，比如，跟时间、随机数有关的代码。
2. 滥用全局变量。类变量。
3. 滥用静态方法。主要原因是静态方法也很难 mock。但是，这个要分情况来看。只有在这个静态方法执行耗时太长、依赖外部资源、逻辑复杂、行为未决等情况下，我们才需要在单元测试中 mock 这个静态方法。除此之外，如果只是类似 Math.abs() 这样的简单静态方法，并不会影响代码的可测试性，因为本身并不需要 mock。
4. 使用复杂继承。如果我们利用组合而非继承来组织类之间的关系，类之间的结构层次比较扁平，在编写单元测试的时候，只需要 mock 类所组合依赖的对象即可。
5. 高耦合代码。

##### 3.3.3.4. 讨论
1. 提供方法的类不要new，也就是我们常说的service类，这个是要依赖注入的。提供属性的类，比如vo，bo，entity这些就可以new。

#### 3.3.4. 通过封装、抽象、模块化、中间层等【解耦】代码（大型重构）

##### 3.3.4.1. “解耦”为何如此重要？
1. 利用解耦的方法对代码重构，就是保证代码不至于复杂到无法控制的有效手段。
3. 提高代码的可读性和可维护性。
2. “高内聚、松耦合”的代码可测试性也更加好，容易 mock 或者很少需要 mock 外部依赖的模块或者类。
4. 代码“高内聚、松耦合”，也就意味着，代码结构清晰、分层和模块化合理、依赖关系简单、模块或类之间的耦合小，那代码整体的质量就不会差。

##### 3.3.4.2. 如何判定代码是否需要“解耦”？
1. 把模块与模块之间、类与类之间的`依赖关系画出来`，根据依赖关系图的复杂性来判断是否需要解耦重构。
2. 配合间接的衡量标准(修改代码会不会牵一发而动全身)一块来使用。

##### 3.3.4.3. 如何给代码“解耦”？

###### 3.3.4.3.1. 封装与抽象

###### 3.3.4.3.2. 中间层
1. 引入中间层能简化模块或类之间的依赖关系。
2. 四阶段重构：第一阶段：引入一个中间层，包裹老的接口，提供新的接口定义；第二阶段：新开发的代码依赖中间层提供的新接口；第三阶段：将依赖老接口的代码改为调用新接口；第四阶段：确保所有的代码都调用新接口之后，删除掉老的接口。

###### 3.3.4.3.3. 模块化
1. 模块化是构建复杂系统常用的手段。对于一个大型复杂系统来说，没有人能掌控所有的细节。
2. 在开发代码的时候，一定要有模块化意识，将每个模块都当作一个独立的 lib 一样来开发，只提供封装了内部实现细节的接口给其他模块使用，这样可以减少不同模块之间的耦合度。

###### 3.3.4.3.4. 其他设计思想和原则
1. 单一职责原则、基于接口而非实现编程、依赖注入、多用组合少用继承、迪米特法则等。
2. 还有一些设计模式，比如观察者模式。

##### 3.3.4.4. 讨论
1. `Spring 中的 AOP 能实现业务与非业务代码的解耦，IOC 能实现对象的构造和使用的解耦`。

### 3.4. 编程规范

1. 解决可读性的问题。
2. 比较偏向于记忆，你只要照着来做可以。

#### 3.4.1. 命名(主要目的是解释“做什么”)和注释
1. 对于影响范围比较大的命名，比如包名、接口、类名，我们一定要反复斟酌、推敲。实在想不到好名字的时候，可以去 GitHub 上用相关的关键词联想搜索一下，看看类似的代码是怎么命名的。

##### 3.4.1.1. 命名多长（以能准确达意为目标，换位思考）
1. 对于一些默认的、大家都比较熟知的词，用缩写。sec 表示 second、str 表示 string、num 表示 number、doc 表示 document。
2. 对于作用域比较小的变量，我们可以使用相对短的命名。
3. 对于类名这种作用域比较大的，用长的命名方式。

##### 3.4.1.2. 利用上下文简化命名
1. 类的成员变量。
2. 函数的参数。

##### 3.4.1.3. 命名要可读、可搜索
1. 不要用一些特别生僻、难发音的英文单词来命名。
2. 最好能符合整个项目的命名习惯。大家都用“selectXXX”表示查询，你就不要用“queryXXX”；大家都用“insertXXX”表示插入一条数据，你就要不用“addXXX”，统一规约是很重要的，能减少很多不必要的麻烦。

##### 3.4.1.4. 命名接口和抽象类
1. 对于接口的命名，一般有两种比较常见的方式。一种是加前缀“I”，表示一个 Interface。比如 IUserService，对应的实现类命名为 UserService。另一种是不加前缀，比如 UserService，对应的实现类加后缀“Impl”，比如 UserServiceImpl。
2. 对于抽象类的命名，也有两种方式，一种是带上前缀“Abstract”，比如 AbstractConfiguration；另一种是不带前缀“Abstract”。
3. `只要项目里能够统一就行`。

##### 3.4.1.5. 注释写什么
```java
/**
* (what) Bean factory to create beans. 
* 
* (why) The class likes Spring IOC framework, but is more lightweight. 
*
* (how) Create objects from different sources sequentially:
* user specified object > SPI > configuration > default object.
*/
public class BeansFactory {
  // ...
}
```
1. 注释起到总结性作用、文档的作用.
2. 对于有些比较复杂的类或者接口，我们可能还需要在注释中写清楚“如何用”，举一些简单的 quick start 的例子，让使用者在不阅读代码的情况下，快速地知道该如何使用。
3. 代码中间插入总结性的注释来让代码结构更清晰、更有条理。

##### 3.4.1.6. 注释的经验
1. `类和函数一定要写注释`，而且要写得尽可能全面、详细，而函数内部的注释要相对少一些，一般都是靠好的命名、提炼函数、解释性变量、总结性注释来提高代码的可读性。

#### 3.4.2. 代码风格（Code Style）
1. 在团队、项目中保持风格统一，让代码像同一个人写出来的，整齐划一。

##### 3.4.2.1. 类、函数多大才合适？
1. 间接的判断标准，那就是，当一个类的代码读起来让你感觉头大了，实现某个功能时不知道该用哪个函数了，想用哪个函数翻半天都找不到了，只用到一个小功能要引入整个类（类中包含很多无关此功能实现的函数）的时候，这就说明类的行数过多了。

##### 3.4.2.2. 一行代码多长最合适？
1. 一行代码最长不能超过 IDE 显示的宽度。

##### 3.4.2.3. 善用空行分割单元块
1. 在代码之间、类的成员变量与函数之间、静态成员变量与普通成员变量之间、各函数之间、甚至各成员变量之间，我们都可以通过添加空行的方式，让这些不同模块的代码之间，界限更加明确。

##### 3.4.2.4. 四格缩进还是两格缩进？
1. 跟业内推荐的风格统一、跟著名开源项目统一。
2. 推荐2格。

##### 3.4.2.5. 大括号是否要另起一行？
1. 将括号放到跟语句同一行。

##### 3.4.2.6. 类中成员的排列顺序
1. Java 类文件中，先要书写类所属的包名，然后再罗列 import 引入的依赖类。在 Google 编码规范中，依赖类按照字母序从小到大排列。
2. 在类中，成员变量排在函数的前面。成员变量之间或函数之间，都是按照“先静态（静态函数或静态成员变量）、后普通（非静态函数或非静态成员变量）”的方式来排列的。除此之外，成员变量之间或函数之间，还会按照作用域范围从大到小的顺序来排列，先写 public 成员变量或函数，然后是 protected 的，最后是 private 的。

#### 3.4.3. 编程技巧


