---
layout: post
title: Mockito 简明教程
date: 2016-02-18 02:41
author: admin
comments: true
categories: [Mockito,TDD,Java]
tags: [Mockito,TDD,简明教程,入门,Mock,Java]
---
Mock 测试是单元测试的重要方法之一。本文介绍了基于 Java 语言的 Mock 测试框架 -- Mockito 的使用。

<!-- more -->

## 什么是 Mock 测试

Mock 测试就是在测试过程中，对于某些不容易构造（如 HttpServletRequest 必须在Servlet 容器中才能构造出来）或者不容易获取比较复杂的对象（如 JDBC 中的ResultSet 对象），用一个虚拟的对象（Mock 对象）来创建以便测试的测试方法。

Mock 最大的功能是帮你把单元测试的耦合分解开，如果你的代码对另一个类或者接口有依赖，它能够帮你模拟这些依赖，并帮你验证所调用的依赖的行为。
 
比如一段代码有这样的依赖：

![](http://media.techtarget.com/tss/static/articles/content/JMockTestDrivenDev/images/image004.jpg)

 
当我们需要测试A类的时候，如果没有 Mock，则我们需要把整个依赖树都构建出来，而使用 Mock 的话就可以将结构分解开，像下面这样：

![](http://media.techtarget.com/tss/static/articles/content/JMockTestDrivenDev/images/image010.jpg)


### Mock 对象使用范畴

真实对象具有不可确定的行为，产生不可预测的效果（如：股票行情，天气预报） ：

* 真实对象很难被创建的
* 真实对象的某些行为很难被触发
* 真实对象实际上还不存在的（和其他开发小组或者和新的硬件打交道）等等

### 使用 Mock 对象测试的关键步骤

* 使用一个接口来描述这个对象
* 在产品代码中实现这个接口
* 在测试代码中实现这个接口
* 在被测试代码中只是通过接口来引用对象，所以它不知道这个引用的对象是真实对象，还是 Mock 对象。
 
## Mock 与 Stub 的区别

Mock 不是 Stub，两者是有区别的：

* 前者被称为  mockist  TDD，而后者一般称为  classic TDD ；
* 前者是基于行为的验证（behavior verification），后者是基于状态的验证 （state verification）；
* 前者使用的是模拟的对象，而后者使用的是真实的对象。


## Java Mock 测试 

目前，在 Java 阵营中主要的 Mock 测试工具有 [Mockito](http://mockito.org)，[JMock](http://www.jmock.org)，[EasyMock](http://easymock.org/) 等。

关于这些框架的比较，不是本文的重点。本文着重介绍 Mockito 的使用。

## Mockito 的特性

Mockito  是美味的 Java 单元测试  Mock 框架，[开源](https://github.com/mockito)。

大多 Java Mock 库如 EasyMock 或 JMock 都是 expect-run-verify （期望-运行-验证）方式，而 Mockito 则使用更简单，更直观的方法：在执行后的互动中提问。使用 Mockito，你可以[验证任何你想要的](http://monkeyisland.pl/2008/02/24/can-i-test-what-i-want-please)。而那些使用 expect-run-verify 方式的库，你常常被迫查看无关的交互。

[非 expect-run-verify 方式](http://monkeyisland.pl/2008/02/01/deathwish/) 也意味着，Mockito 无需准备昂贵的前期启动。他们的目标是透明的，让开发人员专注于测试选定的行为。

Mockito 拥有的非常少的 API，所有开始使用 Mockito，几乎没有时间成本。因为只有一种创造 mock 的方式。只要记住，在执行前 stub，而后在交互中验证。你很快就会发现这样 TDD java 代码是多么自然。

[类似 EasyMock 的语法](https://github.com/mockito/mockito/wiki/Mockito-vs-EasyMock)来的，所以你可以放心地重构。Mockito 并不需要“expectation（期望）”的概念。只有 stub 和验证。

Mockito 实现了 [Gerard Meszaros](http://xunitpatterns.com/Mocks,%20Fakes,%20Stubs%20and%20Dummies.html) 所谓的 Test Spy.

### 其他的一些特点：

* 可以 mock 具体类而不单止是接口
* 一点注解语法糖 - `@Mock`
* 干净的验证错误是 - 点击堆栈跟踪，看看在测试中的失败验证;点击异常的原因来导航到代码中的实际互动。堆栈跟踪总是干干净净。
* 允许灵活有序的验证（例如：你任意有序 `verify`，而不是每一个单独的交互）
* 支持“详细的用户号码的时间”以及“至少一​​次”验证
* 灵活的验证或使用参数匹配器的 stub （`anyObject()`，`anyString()` 或 `refEq()` 用于基于反射的相等匹配）
* 允许创建[自定义的参数匹配器](http://mockito.googlecode.com/svn/branches/1.6/javadoc/org/mockito/Matchers.html)或者使用现有的 hamcrest 匹配器
 
## Mockito 入门

### 声明 mockito 依赖

Gradle 用户可以使用：

    repositories { jcenter() }
    dependencies { testCompile "org.mockito:mockito-core:1.+" }

Maven 用户可以使用：<http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.mockito%22%2C%20a%3A%22mockito-core%22>

Mockito 自动发布到 <http://jcenter.bintray.com/org/mockito/mockito-core/>
并同步到 Maven Central Repository

### 示例

#### 1.验证行为

    //Let's import Mockito statically so that the code looks clearer
     import static org.mockito.Mockito.*;
    
     //mock creation
     List mockedList = mock(List.class);
    
     //using mock object
     mockedList.add("one");
     mockedList.clear();
    
     //verification
     verify(mockedList).add("one");
     verify(mockedList).clear();
 
一旦创建 mock 将会记得所有的交互。你可以选择验证你感兴趣的任何交互

#### 2.stubbing

     //You can mock concrete classes, not just interfaces
     LinkedList mockedList = mock(LinkedList.class);
    
     //stubbing
     when(mockedList.get(0)).thenReturn("first");
     when(mockedList.get(1)).thenThrow(new RuntimeException());
    
     //following prints "first"
     System.out.println(mockedList.get(0));
    
     //following throws runtime exception
     System.out.println(mockedList.get(1));
    
     //following prints "null" because get(999) was not stubbed
     System.out.println(mockedList.get(999));
    
     //Although it is possible to verify a stubbed invocation, usually it's just redundant
     //If your code cares what get(0) returns, then something else breaks (often even before verify() gets executed).
     //If your code doesn't care what get(0) returns, then it should not be stubbed. Not convinced? See here.
     verify(mockedList).get(0);

* 默认情况下，所有方法都会返回值，一个 mock 将返回要么 null，一个原始/基本类型的包装值或适当的空集。例如，对于一个 int/Integer  就是 0，而对于 boolean/Boolean 就是 false。
* Stubbing 可以被覆盖。
* 一旦 stub，该方法将始终返回一个 stub 的值，无论它有多少次被调用。
* 最后的 stubbing 是很重要的 - 当你使用相同的参数 stub 多次同样的方法。换句话说：stubbing 的顺序是重要的，但它唯一有意义的却很少，例如当 stubbing 完全相同的方法调用，或者有时当参数匹配器的使用，等等。

#### 3.参数匹配器

Mockito 验证参数值使用 Java 方式：通过使用 equals() 方法。有时，当需要额外的灵活性，可以使用参数匹配器：

     //stubbing using built-in anyInt() argument matcher
     when(mockedList.get(anyInt())).thenReturn("element");
    
     //stubbing using custom matcher (let's say isValid() returns your own matcher implementation):
     when(mockedList.contains(argThat(isValid()))).thenReturn("element");
    
     //following prints "element"
     System.out.println(mockedList.get(999));
    
     //you can also verify using an argument matcher
     verify(mockedList).get(anyInt());

参数匹配器允许灵活的验证或 stubbing。点击[这里](http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html)查看更多内置的匹配器和自定义的参数匹配器/ hamcrest匹配器的例子。

自定义参数的匹配信息，请查看 Javadoc 中 [ArgumentMatcher](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html) 类。
 
如果你正在使用参数的匹配，所有的参数都由匹配器来提供。

下面的示例演示验证，但同样适用于 stubbing：


    verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
    //above is correct - eq() is also an argument matcher
    
    verify(mock).someMethod(anyInt(), anyString(), "third argument");
    //above is incorrect - exception will be thrown because third argument is given without an argument matcher.
 
 
#### 4.调用额外的调用数字/at least x / never

    //using mock
    mockedList.add("once");
    
    mockedList.add("twice");
    mockedList.add("twice");
    
    mockedList.add("three times");
    mockedList.add("three times");
    mockedList.add("three times");
    
    //following two verifications work exactly the same - times(1) is used by default
    verify(mockedList).add("once");
    verify(mockedList, times(1)).add("once");
    
    //exact number of invocations verification
    verify(mockedList, times(2)).add("twice");
    verify(mockedList, times(3)).add("three times");
    
    //verification using never(). never() is an alias to times(0)
    verify(mockedList, never()).add("never happened");
    
    //verification using atLeast()/atMost()
    verify(mockedList, atLeastOnce()).add("three times");
    verify(mockedList, atLeast(2)).add("five times");
    verify(mockedList, atMost(5)).add("three times");


times(1) 是默认的，因此，使用的 times(1) 可以显示的省略。

#### 5.Stubbing void 方法处理异常


    doThrow(new RuntimeException()).when(mockedList).clear();
    
    //following throws RuntimeException:
    mockedList.clear();
    
#### 6.有序的验证

    // A. Single mock whose methods must be invoked in a particular order
    List singleMock = mock(List.class);
    
    //using a single mock
    singleMock.add("was added first");
    singleMock.add("was added second");
    
    //create an inOrder verifier for a single mock
    InOrder inOrder = inOrder(singleMock);
    
    //following will make sure that add is first called with "was added first, then with "was added second"
    inOrder.verify(singleMock).add("was added first");
    inOrder.verify(singleMock).add("was added second");
    
    // B. Multiple mocks that must be used in a particular order
    List firstMock = mock(List.class);
    List secondMock = mock(List.class);
    
    //using mocks
    firstMock.add("was called first");
    secondMock.add("was called second");
    
    //create inOrder object passing any mocks that need to be verified in order
    InOrder inOrder = inOrder(firstMock, secondMock);
    
    //following will make sure that firstMock was called before secondMock
    inOrder.verify(firstMock).add("was called first");
    inOrder.verify(secondMock).add("was called second");
    
    // Oh, and A + B can be mixed together at will
    
有序验证是为了灵活 - 你不必一个接一个验证所有的交互。

此外，您还可以通过创建 InOrder 对象传递只与有序验证相关的 mock 。

#### 7. 确保 mock 上不会发生交互


    //using mocks - only mockOne is interacted
    mockOne.add("one");
    
    //ordinary verification
    verify(mockOne).add("one");
    
    //verify that method was never called on a mock
    verify(mockOne, never()).add("two");
    
    //verify that other mocks were not interacted
    verifyZeroInteractions(mockTwo, mockThree);

#### 8.寻找多余的调用

    //using mocks
    mockedList.add("one");
    mockedList.add("two");
    
    verify(mockedList).add("one");
    
    //following verification will fail
    verifyNoMoreInteractions(mockedList);
    
    
注意：不建议 verifyNoMoreInteractions() 在每个测试方法中使用。 verifyNoMoreInteractions() 是从交互测试工具包一个方便的断言。只有与它的相关时才使用它。滥用它导致难以维护。

#### 9. 标准创建 mock 方式 - 使用 `@Mock` 注解

* 最小化可重用 mock 创建代码
* 使测试类更加可读性
* 使验证错误更加易读，因为字段名称用于唯一识别 mock

    public class ArticleManagerTest {
    
       @Mock private ArticleCalculator calculator;
       @Mock private ArticleDatabase database;
       @Mock private UserProvider userProvider;
    
       private ArticleManager manager;

在基础类或者测试 runner 里面，使用如下：

    MockitoAnnotations.initMocks(testClass);

可以使用内建  runner: [MockitoJUnitRunner](http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html) 或者 rule: [MockitoRule](http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html)

更多详见 [MockitoAnnotations](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html)

#### 10. Stubbing 连续调用（迭代器式的 stubbing）

    when(mock.someMethod("some arg"))
     .thenThrow(new RuntimeException())
     .thenReturn("foo");
    
    //First call: throws runtime exception:
    mock.someMethod("some arg");
    
    //Second call: prints "foo"
    System.out.println(mock.someMethod("some arg"));
    
    //Any consecutive call: prints "foo" as well (last stubbing wins).
    System.out.println(mock.someMethod("some arg"));
    
下面是一个精简版本：

    when(mock.someMethod("some arg"))
     .thenReturn("one", "two", "three");
     
#### 11. 回调 Stubbing 

允许使用泛型 [Answer](http://site.mockito.org/mockito/docs/current/org/mockito/stubbing/Answer.html) 接口。

然而，这是不包括在最初的 Mockito 另一个有争议的功能。我们建议您只需用thenReturn() 或 thenThrow() 来 stubbing ，这在测试/测试驱动中应用简洁与简单的代码足够了。但是，如果你有一个需要 stub 到泛型 Answer 接口，这里是一个例子：


    when(mock.someMethod(anyString())).thenAnswer(new Answer() {
       Object answer(InvocationOnMock invocation) {
           Object[] args = invocation.getArguments();
           Object mock = invocation.getMock();
           return "called with arguments: " + args;
       }
    });
    
    //the following prints "called with arguments: foo"
    System.out.println(mock.someMethod("foo"));
    
#### 12. doReturn()|doThrow()| doAnswer()|doNothing()|doCallRealMethod()  家族方法

Stubbing void 方法，需要不同的 when(Object) ，因为编译器不喜欢括号内无效的方法...

在  用于 Stubbing void 方法中，doThrow(Throwable...)  取代 stubVoid(Object)。主要原因是提高可读性和与 doAnswer()  保持一致性。

当你想用 stub void 方法 使用 doThrow（）：

    doThrow(new RuntimeException()).when(mockedList).clear();
    
    //following throws RuntimeException:
    mockedList.clear();
    
在调用 when() 的相应地方可以使用 oThrow(), doAnswer(), doNothing(), doReturn() 和 doCallRealMethod()，当：

* stub void 方法
* stub 方法在 spy 对象(见下面)
* 可以不止一次的 stub 相同的方法，在测试的中期来改变 mock 的行为

但你更加倾向于使用这些方法来代替 when()，在所有的 stubbing 调用。可以阅读更多关于这些方法的描述：

[doReturn(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doReturn(java.lang.Object))

[doThrow(Throwable...)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doThrow(java.lang.Throwable...))

[doThrow(Class)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doThrow(java.lang.Class))

[doAnswer(Answer)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doAnswer(org.mockito.stubbing.Answer))

[doNothing()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doNothing())

[doCallRealMethod()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doCallRealMethod())
   
## 参考

* <http://martinfowler.com/articles/mocksArentStubs.html>
* <http://mockito.org/>