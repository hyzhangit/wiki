
## 背景

在使用PowerMock进行UT测试时发现公共打桩较为困难，主要在于使用PowerMockito方式PrepareForTest注解使用必须加在测试类上，导致公共打桩无论是通过集成还是组合的方式都存在对于被测试类的方向依赖，测试类于公共桩之间存在耦合。

PowerMock提供了MockPolicy的方式，通过在类加载前改变字节码的方式完成方法的打桩，可以实现公共打桩的独立性和插件化。
但是该方式实现的打桩类无法与PowerMockito配合使用，不能在测试用例中进行打桩的调整，且写法没有PowerMockito简洁。

## 思考

能否实现一个使用PowerMockito工具打桩，可以通过类和方法注解注入，又不依赖于测试类来添加PrepareForTest的方式呢？

## 探索

通过对PowerMock运行流程和源码的探索，发现UT用例是从PowerMockRunner为入口进行后续的一系列类加载、打桩等操作。

1. PowerMock执行时非常核心的一个点是PowerMock使用的类加载器，JavasistMockLoader该加载器用于对测试用例打桩类型的加载和实例化等，其中该加载器是由MockFactory进行创建的，创建时依赖的Perpare类就负责关PrepareForTest注解的解析。

2. PowerMock支持注入Listener接口的方式在测试类的Test方法和Before方法运行前进行自定义初始化（@Mock初始化就是通过AnnotationListener完成的），

## 设计

1. 由于Prepare类是在MockFactory中使用的，MockFactory又是由一系列类调用的，调用层级很深，如果重写MockFactory的话就需要重写级联的一系列类，成本太高，所以考虑使用字节码动态代理的方式来完成增强，通过对Prepare获取PrepareForTest方法的字节码改造，实现了在解析测试类时将注入类的PrepareForTest一并进行加载，由此实现公共桩PrepareForTest与测试类的解耦。

2. 公共桩的打桩实现则可以通过自定义Listener并注入的方式类实现。

## 实现

1. 定义一个注解来支持公共桩实现类的注入--@InjectMocker。

2. 由于入口在PowerMockRunner，想要在类加载前改变字节码，则需要重写该Runner---InjectMockRunner。

3. 注入公共桩为了符合PowerMock语法，没有设置任何接口，只要符合PowerMock UT的用例都可以作为公共桩注入。

## 使用

1. @RunWith(InjectMockRunner)

2. @InjectMocker({Mocker.class})--支持在类和方法上（注解在类上针对所有测试方法生效且只执行一次，注解在方法上针对当前方法生效）



## 公共桩

### 普通接口


### new打桩


### 静态方法

## 注意事项

1. 公共桩一定要有无参构造。
