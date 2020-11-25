# Code Generation in CUBA: What makes the magic
# CUBA 的代码生成：神奇在哪里

Summary: 
Code generation is a common thing in modern frameworks. Like every technology, code generation has application areas and limitations. In this article, we will have a look at code generation usage in CUBA Framework nowadays and discuss the future development of this technique.  
摘要：
现代框架中代码生成是一个普遍的情况。正如每个技术一样，代码生成也有应用范畴和限制。在本文中，我们来看一下目前 CUBA 框架中代码生成的使用，并且讨论下这项技术将来的发展。

## Introduction
## 介绍

Code generation is a common thing in modern frameworks. There may be different reasons behind introducing code generation - from getting rid of boilerplate code to reflection replacement and creating complex solutions based on domain-specific languages.  
现代框架中代码生成是一个普遍的情况。引入代码生成的原因可能有很多种 - 抛弃制式代码，反射替代，或是基于领域语言制定复杂方案。

Like every technology, code generation has application areas and limitations. In this article, we will have a look at code generation usage in CUBA Framework nowadays and discuss the future development of this technique.  
正如每个技术一样，代码生成也有应用范畴和限制。在本文中，我们来看一下目前 CUBA 框架中代码生成的使用，并且讨论下这项技术将来的发展。

## What is generated in CUBA?
## CUBA 中哪些代码是生成的？
CUBA framework is built over the well-known Spring framework. Basically, every CUBA application can be treated as a Spring application with some additional APIs added in order to simplify the development of the common business functionality.  
CUBA 框架是基于流行的 Spring 框架建立的。基本上，每个 CUBA 应用都可以当作一个 Spring 应用，它附带有额外的 API，这些 API 的目的是简化通用的业务功能的开发工作。

CUBA provides a Vaadin-based library for quick UI development. And this library uses declarative data binding approach. Therefore, it is possible to display different object property values in the same UI widget by switching binding in runtime.  
CUBA 提供了一个基于 Vaadin 的用来快速开发 UI 的库。这个库使用声明式的数据绑定方案。因此，通过运行时调整绑定，便可以在同一 UI 部件上展示不同的对象属性。

It means that every object should be able to provide a property value by its string name. Having the Spring framework in the core means that reflection can be used easily to fetch property values.  
这意味着每个对象需要能提供一个基于其字符串名称的属性值。在 core 模块使用 Spring 框架，意味着可以简单的通过反射获取属性值。

### Entities Enhancement
### 实体增强
Reflection is a powerful thing, but it is still slow despite all the optimization efforts of the JVM team. And when we talk about UI, especially displaying big data grids, we come to the conclusion that reflective methods will be invoked pretty frequently. E.g. displaying 20 lines with 10 properties easily leads to 200 invocations. Let’s multiply it by amount of users and take into account that all these invocations will happen on the app server (it’s the way Vaadin works), and we may get a pretty good workload for the server.  
反射是很强大，但它会慢，尽管优化工作是 JVM 团队的责任。当我们讨论 UI 时，尤其是展示大量数据的网格，我们可以推测出反射的方法会被相当频繁地调用。例如，展示20行数据，每行10个属性，轻松地达到200次调用。我们再将这个数字乘以用户数量，再考虑所有这些调用都会发生在应用服务器上（ Vaadin 是这样的 ），可见对服务器是相当大的负载。

So, for every data object (entity) class we need to define a simple method that will invoke a property getter (or setter) based on the property name. The simple switch statement will do.  
所以，对于每个数据对象（实体）类，我们需要定义一个简单的方法来基于属性名称调用属性的 getter (或 setter) 方法。简单的 switch 语句便可实现。

Also, the UI needs to “know” if an object was changed, so that a user could confirm data changes when the screen is closed. So, we need to inject a state listener into every object’s setter to check if the property’s value has changed.  
另外，UI 需要 “知道” 一个对象是否被修改了，以便界面关闭时用户可以确认数据的修改。所以，我们需要注入一个状态监听器到对象的每个 setter 方法中来检查属性值是否被修改。

And in addition to the method that sets or gets property values, we need to update every setter and invoke a state change listener to mark the entity object as changed.  
除了 set 和 get 属性值的方法，我们需要修改每个 setter 方法来调用一个状态修改的监听器来标记实体对象为已修改。

This method is also not complex, basically one-line. But it would be unfair to ask a developer to do the boring job - adding and updating a bunch of very simple methods for every entity’s property. And that’s exactly the case where code generation shines.  
这个方法也不复杂，基本上一行就够了。但是让开发人员来做这个无聊的工作是不公平的 - 对每个实体属性都添加/修改一大堆简单的方法。这便正是代码生成闪亮登场的情形。

CUBA uses EclipseLink ORM under the hood. And this framework solves some tasks stated earlier. As it is said in the [documentation](https://www.eclipse.org/eclipselink/documentation/2.7/concepts/app_dev005.htm): “The EclipseLink JPA persistence provider uses weaving to enhance both JPA entities and Plain Old Java Object (POJO) classes for such things as lazy loading, change tracking, fetch groups, and internal optimizations.”  
CUBA 的幕后有 EclipseLink ORM。这个框架完成了刚才提到的一些任务。正如它的 [文档](https://www.eclipse.org/eclipselink/documentation/2.7/concepts/app_dev005.htm) 提到的：“ EclipseLink JPA persistence provider 使用织入来增强 JPA 实体 和 POJO 类的下列方面：懒加载，修改监测，获取组，以及内部优化。”


In CUBA, EclipseLink’s static weaving is invoked in build-time (it is executed at run-time by default) by the CUBA build plugin.  
在 CUBA 中，EclipseLink 的静态织入是 CUBA 构建插件在构建时调用的（在运行时默认执行）。

In addition to this, invoking change listeners is still a task that should be resolved by CUBA. And it is done by the build plugin at build-time. So, if you open an entity’s `.class` file, you can see a number of methods that you don’t see in your source code. And you may be surprised to see how your setters changed. For example, instead of:  
另外，调用修改监听器还是需要 CUBA 来完成的任务。 它是在构建时被构建插件完成的。所以，如果你打开一个实体的 `.class` 文件，你会看到不少在你的源代码中不存在的方法。你也会惊讶于 setter 方法有哪些变化。例如，下面这样的源码：

```
public void setName(String name) {
   this.name = name;
}
```
In the decompiled code you will see:  
在反编译的代码里你会看到：

```
public void setName(String name) {
   String __prev = this.getName();
   this._persistence_set_name(name);
   Object var5 = null;
   String __new = this.getName();
   if (!InstanceUtils.propertyValueEquals(__prev, __new)) {
       this.propertyChanged("name", __prev, __new);
   }
}
```

This is the mix of code generated by EclipseLink weaver and CUBA build plugin. So, in CUBA compiled entity classes are different from what you actually write in the IDE.  
这里混合了 EclipseLink 织入的代码和 CUBA 构建插件生成的代码。因此，在 CUBA 中编译后的实体 class 文件与你实际在 IDE 中编写的是不同的。

### Bean Validation Messages
### Bean 验证消息
CUBA Platform supports internationalization for the bean validation messages. It means that in JPA validation annotations you can refer to `.properties` file entry instead of writing the message string directly into the annotation value.  
CUBA 平台支持 bean 验证消息的国际化。意思是在 JPA 验证标注中你可以指定 `.properties` 文件，而不是直接在标注值中写下消息字符串。

In the code, it looks like this:  
源代码看起来是这样：

```
@NotNull(message = "{msg://hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```

Translation resource files for entities should be in the same package as entities. So, if we want to simplify loading property values, we need to specify the package name in this line. The action is simple, the update algorithm is clear, so it was decided to use code generation.  
实体对应的翻译的资源文件应该与实体在同一个包下。所以，如果我们想简化加载属性值过程，我们需要在这一行指定包名。这个动作简单，更新的算法也清晰，所以决定用代码生成来实现它。

CUBA Platform plugin transforms the message reference above into the format:  
CUBA 插件将上面的消息引用转为如下格式：

```
@NotNull(message = "{msg://com.company.hr/hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```
And now we have the package name, therefore, fetching the message from the resource file using `getResourceAsStream()` method is much simpler.  
现在我们得到了包名，所以，使用 `getResourceAsStream()` 从资源文件中获取消息则简单多了。

## What is the Future?
## 将来会如何？
There is not too much code generated at the moment, but the framework is evolving. The development team is thinking about using code generation for other cases.  
目前没有很多的代码生成，但是框架在进化。开发团队正在考虑在其他情形下使用代码生成。

### Common Entity Methods
### 通用的实体方法
At the moment, in CUBA the entity structure is flexible, but it is based on interfaces, so you need to implement methods defined in those interfaces. For example, if you want your entity to support soft delete, you need to implement the following interface:  
目前，CUBA 的实体结构是灵活的，但是基于接口，所以，你需要实现接口方法。例如，如果想让你的实体支持软删除，你需要实现下面的接口：

```
public interface SoftDelete {
   Date getDeleteTs();
   String getDeletedBy();
   //More methods here
}

```
Of course, there are default implementations of those interfaces like `com.haulmont.cuba.core.entity.StandardEntity`, so you can extend this entity in order to use the implementation.  
当然，这些接口都有 `com.haulmont.cuba.core.entity.StandardEntity` 这样默认的实现，所以你想用这些默认实现的时候可以扩展这个实体。

But it would be much simpler to use method names that are not hardcoded and just mark properties that you want to use to store the delete date and a username of who performed the delete. In this case, we could generate the methods shown above and map invocations to proper getters and setters. Let’s have a look at an example of an entity:  
但是，不通过方法名写死的方式，而是仅仅通过标记删除时间和删除人的属性来实现会更简单。这种情形下，我们可以生成上述方法并且调用合适的 getter 和 setter 方法。我们来看一个实体的例子：

```
@Entity
public class Account {

   //Other fields
   @DeletedDate
   private Date disposedAt;

   @DeletedBy
   private String disposedBy;

   public Date getDisposedAt() {
       return disposedAt;
   }

   public String getDisposedBy() {
       return disposedBy;
   }

}
```
In this entity, you can see special fields defined to store the data about the delete process. So, what will we see if we apply some enhancement to this entity?  
在这个实体中，你会看到特别的属性被定义为储存删除操作的数据。所以，如果我们对这个实体进行增强会得到什么？

```
@Entity
public class Account implements SoftDelete {

   //Other fields
   @DeletedDate
   private Date disposedAt;

   @DeletedBy
   private String disposedBy;

   public Date getDisposedAt() {
       return disposedAt;
   }

   public String getDisposedBy() {
       return disposedBy;
   }

   //Generated
   @Override
   public Date getDeleteTs() {
       return getDisposedAt();
   }

   //Generated
   @Override
   public String getDeletedBy() {
       return getDisposedBy();
   }
}
``` 
Now we can check if the instance supports soft delete by applying the `instanceof` operator, thus implementing a generic approach for soft delete operations within the framework relying only upon the framework’s interfaces and methods instead of detecting annotations in runtime.  
现在，我们可以通过 `instanceof` 操作来查看实例是否支持软删除，这样，在框架中实现一个通用的软删除操作的方案仅仅依赖框架的接口和方法，而不是在运行时检测标注。

This approach will add more flexibility to entities’ definition, especially in database reverse engineering.  
这个方式会给实体定义带来更多灵活性，尤其是数据库逆向工程。

So, in future CUBA versions, we plan to add more bits and pieces of code generation here and there to make a developer’s life easier.  
所以，在将来的 CUBA 版本中，我们计划在各处零星地增加一些代码生成，让开发者更轻松。

## Build-time generation vs runtime generation
## 构建时生成 vs 运行时生成
As you may notice, in CUBA, code generation happens at build-time. There are pros and cons for this approach, let’s describe them.  
你可能注意到了，在 CUBA 中，代码生成发生在构建时。这种方式有利有弊，我们来分析一下。

Build-time generation allows you to catch problems at earlier stages. When you generate code, there are many “variables” that you should take into account. For example, if EclipseLink API changes, then the invocations generated by CUBA during the enhancement process will become invalid. JDK API changes may cause issues, too. By generating code during build-time we rely on the Java compiler to find such issues at early stages. And compile-time errors are usually easier to find than runtime ones, source code is a static thing. Even if it is generated.  
构建时生成可以在更早的阶段发现问题。当代码生成时，有许多 “变量” 需要考虑。例如，如果 EclipseLink API 改变了，那么 CUBA 在增强的过程中调用会变得无效。同样，JDK API 的变化也会带来问题。在构建时生成代码，我们依赖 Java 编译器在更早的阶段发现这类问题。另外，编译时的错误总是早于运行时错误被发现，源代码是静态的，即使是生成的也一样。

But build-time generation requires a separate tool that is not a part of the project codebase - build plugin. Introducing one more tool means introducing one more point of failure. A developer now depends on a compiler and code generation tool. And if any of them will contain a bug - there is a problem, because a developer cannot update them.  
但是构建时生成需要一个特别的工具，它不是项目的代码部分 - 构建插件。引入多一个工具，意味着引入多一点失败的可能。开发人员会依赖于编译器和代码生成工具。如果这之中任何一方有个 bug - 便会出问题，开发人员也不能直接修改它们。

In runtime, there is no separate tool, so a code generator is part of the framework. But generation happens at runtime and developers depend on the program state and the VM state. Sometimes dynamic code generation may fail suddenly due to memory consumption or other issues because it is quite hard to control the VM state completely.  
在运行时，便不需要特别的工具，代码生成器是框架的一部分。但是代码生成发生在运行时，开发人员依赖于程序运行状态和 VM 状态。有时候，因为很难完全控制 VM 状态，由于内存开销或其他问题，动态代码生成会突然失败。

So, for CUBA we've chosen code generation at build-time. The amount of generated code is not that huge, the set of classes is limited to entities only, so for this particular case the code generator was pretty simple and there were no blocking issues with it so far.  
所以，CUBA 选择了在构建时生成代码。生成的代码量并不庞大，class 的集合仅仅限制于实体，对于这项特别的情况，代码生成比较简单，目前为止还没发生阻塞性的问题。

## Generation tools
## 生成工具
In Java, a standardized code generation approach appeared starting from Java 5, and it was annotation processing. The idea is simple - you create a processor that can generate new code based on the annotations in the existing code. And you can generate code with annotations that may trigger another processing cycle.  
Java 中，标准的代码生成方案始于 Java 5， 是有关标注处理的。当时的想法很简单 - 你创建一个可以基于现有代码中的标注生成新代码的处理器。你也能通过标注生成代码并触发其他的处理逻辑。

Standard annotation processor has a limitation - it cannot update the existing code, just generate a new one. So, for CUBA, a Javassist library was used.  
标准的标注处理有个限制 - 它不能更改现有代码，只是生成新的。所以，CUBA使用了一个 Javassist 库。

This library allows existing code updates, and it can use plain strings. For example, this code stores the previous value of the property before a setter invocation:  
这个库运行更改现有代码，他能使用简单的字符串。例如，下面的代码在调用 setter 之前将属性的旧值保存下来：
```
ctMethod.insertBefore(
       "__prev = this." + getterName + "();"
);
```
Javassist contains its own limited Java compiler to verify code correctness. Using strings for code generation doesn’t provide type safety, so some bugs can be introduced due to a mistype. But it is much simpler than using a library that uses a typed model for the code generation like ByteBuddy. You literally can see the code that will be added to your classes.  
Javassist 包含限制于它自己的 Java 编译器，用来验证代码的正确性。在代码生成中使用字符串不能保证类型安全，所以一些 bug 就是类型错误引起的。但是使用 ByteBuddy 那样的基于类型模型的代码生成库会更简单。你从字面上会看到加入到 class 文件的代码。

## Conclusion
## 总结
Code generation is a very powerful tool that helps developers to:
1. Avoid doing boring jobs like writing simple repetitive code
2. Automate some methods update due to code changes

代码生成是个强大的工具，可以帮助开发人员：
1. 免去类似编写重复代码的无聊的工作
2. 代码修改后，自动更改某些方法

On the other side, your program is not what you wrote. Extensive code generation can change your sources completely, so you’ll have to debug not your code, but someone else’s.  
另一方面，你的程序并不是完全跟你写的一样。扩展的代码生成会完全的改变你的源码，所以你可能不得不调试别人的代码，而不是你自己的。

In addition to that, you become dependent on the framework’s code generators, and in case of bugs, you have to wait for the plugin update.  
另外，你会依赖于框架的代码生成，万一出现 bug ，你不得不等待插件更新。

In CUBA, code generation areas are limited to entities, and we plan to extend this area slowly to simplify developers' work and add more flexibility to the framework.  
CUBA 中的代码生成仅限于实体，我们计划扩展这一方面，逐渐地简化开发人员的工作，为框架增加更多灵活性。

So, if you’re planning to create your own framework or introduce a code generator for the existing one, consider this technique as very powerful, but fragile. Try to generate simple code and document all the generation steps and conditions, because any change in any API may break the generation easily.  
所以，如果你计划创建自己的框架，或者在已有框架中引入代码生成，考虑一下这项技术的强大，以及。。。脆弱性。试着考虑所有的生成步骤和条件，生成简单的代码和文档，因为任何 API 中的改动都会轻易的破坏生成过程。