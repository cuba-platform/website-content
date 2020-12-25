# CUBA 的代码生成：神奇在哪里

摘要：
现代框架中代码生成是一个普遍的情况。正如每个技术一样，代码生成也有应用范畴和限制。在本文中，我们来看一下目前 CUBA 框架中代码生成的使用，并且讨论下这项技术将来的发展。

## 介绍

现代框架中代码生成是一个普遍的情况。引入代码生成的原因可能有很多种 - 抛弃制式代码，反射替代，或是基于领域特定语言制定复杂方案。

正如每个技术一样，代码生成也有应用范畴和限制。在本文中，我们来看一下目前 CUBA 框架中代码生成的使用，并且讨论下这项技术将来的发展。

## CUBA 中哪些代码是生成的？

CUBA 框架是基于流行的 Spring 框架建立的。基本上，每个 CUBA 应用都可以当作一个 Spring 应用，它附带有额外的 API，这些 API 的目的是简化通用的业务功能的开发工作。

CUBA 提供了一个基于 Vaadin 用来快速开发 UI 的库。这个库使用声明式的数据绑定方案。因此，通过运行时调整数据绑定，便可以在同一 UI 部件上展示不同的对象属性。

这意味着每个对象需要能提供一个基于其字符串名称的属性值。而 CUBA 以 Spring 框架为核心，这样一来可以简单的通过反射获取属性值。

### 实体增强

反射的功能很强大，尽管 JVM 团队花了很多功夫进行优化，但仍然很慢。而在 UI 层面，尤其是展示大量数据的网格，我们可以推测出反射的方法会被相当频繁地调用。例如，展示 20 行数据，每行 10 个属性，轻松地达到 200 次调用。我们再将这个数字乘以用户数量，再考虑所有这些调用都会发生在应用服务器上（ Vaadin 是这样的 ），可见对服务器是相当大的负载。

所以，对于每个数据对象（实体）类，我们需要定义一个简单的方法来基于属性名称调用属性的 getter (或 setter) 方法。简单的 switch 语句便可实现。

另外，UI 需要 “知道” 一个对象是否被修改了，以便界面关闭时用户可以确认数据的修改。所以，我们需要注入一个状态监听器到对象的每个 setter 方法中来检查属性值是否被修改。

除了 set 和 get 属性值的方法，我们需要修改每个 setter 方法来调用一个状态修改的监听器来标记实体对象为已修改。

这个方法也不复杂，基本上一行代码足以。但是让开发人员来做这个无聊的工作是不公平的 - 对每个实体属性都添加/修改一大堆简单的方法。这便正是代码生成闪亮登场的情形。

CUBA 底层使用 EclipseLink ORM。这个框架完成了刚才提到的一些任务。正如它的 [文档](https://www.eclipse.org/eclipselink/documentation/2.7/concepts/app_dev005.htm) 提到的：“ EclipseLink JPA persistence provider 使用织入来增强 JPA 实体 和 POJO 类的下列方面：懒加载，修改监测，获取组，以及内部优化。”

在 CUBA 中，EclipseLink 的静态织入是 CUBA 构建插件在构建时调用的（在运行时默认执行）。

另外，调用修改监听器还是需要 CUBA 来完成的任务。它是在构建时由构建插件完成。所以，如果你打开一个实体的 `.class` 文件，你会看到不少在你的源代码中不存在的方法。你也会惊讶于 setter 方法有哪些变化。例如，下面这样的源码：

```
public void setName(String name) {
   this.name = name;
}
```

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

这里混合了 EclipseLink 织入的代码和 CUBA 构建插件生成的代码。因此，在 CUBA 中编译后的实体 class 文件与你实际在 IDE 中编写的是不同的。

### Bean 验证消息

CUBA 平台支持 bean 验证消息的国际化。意思是在 JPA 验证注解中你可以指定 `.properties` 文件中的键值，而不是直接在注解值中写下消息字符串。

源代码看起来是这样：

```
@NotNull(message = "{msg://hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```

实体对应的翻译的资源文件应该与实体在同一个包下。所以，如果我们想简化加载属性值过程，我们需要在这一行指定包名。这个动作简单，更新的算法也清晰，所以决定用代码生成来实现它。

CUBA 插件将上面的消息引用转为如下格式：

```
@NotNull(message = "{msg://com.company.hr/hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```

现在我们有了包名，使用 `getResourceAsStream()` 从资源文件中获取消息就简单多了。

## 将来会如何？

目前没有很多的代码生成，但是框架在进化。开发团队正在考虑在其他情形下使用代码生成。

### 通用的实体方法

目前，CUBA 的实体结构是灵活的，但是基于接口，所以，你需要实现接口方法。例如，如果想让你的实体支持软删除，你需要实现下面的接口：

```
public interface SoftDelete {
   Date getDeleteTs();
   String getDeletedBy();
   //More methods here
}

```

当然，这些接口都有 `com.haulmont.cuba.core.entity.StandardEntity` 这样默认的实现，所以你想用这些默认实现的时候可以扩展这个实体。

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

现在，我们可以通过 `instanceof` 操作符来检查实例是否支持软删除，这样，在框架中实现一个通用的软删除操作的方案仅仅依赖框架的接口和方法，而不是在运行时检测注解。

这个方式会给实体定义带来更多灵活性，尤其是数据库逆向工程。

所以，在将来的 CUBA 版本中，我们计划在各处零星地增加一些代码生成，让开发者更轻松。

## 构建时生成 vs 运行时生成

你可能注意到了，在 CUBA 中，代码生成发生在构建时。这种方式有利有弊，我们来分析一下。

构建时生成可以在更早的阶段发现问题。当代码生成时，有许多 “变量” 需要考虑。例如，如果 EclipseLink API 改变了，那么 CUBA 在增强过程中生成的调用会变得无效。同样，JDK API 的变化也会带来问题。在构建时生成代码，我们依赖 Java 编译器在更早的阶段发现这类问题。另外，编译时发现错误比运行时容易，因为源代码是静态的，即使是生成的也一样。

但是构建时生成代码需要一个特殊的工具，这个工具不是项目的代码部分 - 构建插件。引入多一个工具，意味着引入多一点失败的可能。开发人员会依赖于编译器和代码生成工具。如果这之中任何一方有个 bug - 便会出问题，因为开发人员也不能直接修改它们。

在运行时，便不需要特别的工具，代码生成器是框架的一部分。但是代码生成发生在运行时，开发人员依赖于程序运行状态和 VM 状态。有时候，因为很难完全控制 VM 状态，由于内存开销或其他问题，动态代码生成会突然失败。

所以，CUBA 选择了在构建时生成代码。生成的代码量并不庞大，目标 class 也仅限于实体，所以对于这种特殊的情况，代码生成比较简单，目前为止还没发生阻塞性的问题。

## 生成工具

Java 中，标准的代码生成方案始于 Java 5，是有关注解处理的。当时的想法很简单 - 你创建一个可以基于现有代码中的注解生成新代码的处理器。你也能通过注解生成代码并触发其他的处理逻辑。

标准的注解处理有个限制 - 不能更改现有代码，只是生成新的。所以，CUBA 使用了 Javassist 库。

这个库可以修改现有代码，并能使用字符串。例如，下面的代码在调用 setter 之前将属性的旧值保存下来：

```
ctMethod.insertBefore(
       "__prev = this." + getterName + "();"
);
```

Javassist 包含了仅限它自己使用 Java 编译器，用来验证代码的正确性。在代码生成中使用字符串不能保证类型安全，所以一些 bug 就是类型错误引起的。但是使用 ByteBuddy 那样的基于类型模型的代码生成库会更简单。你从字面上会看到加入到 class 文件的代码。

## Conclusion

## 总结

代码生成是个强大的工具，可以帮助开发人员：

1. 免去类似编写重复代码的无聊的工作
2. 代码修改后，自动更改某些方法

另一方面，你的程序并不是完全跟你写的一样。大量的代码生成会使你的源码变得面目全非，所以最后你不得不调试这些生成的别人的代码，而不是只调试你自己写的。

除此之外，你会依赖于框架的代码生成，万一出现 bug ，你不得不等待插件更新。

CUBA 中的代码生成目前仅限于实体，我们计划慢慢扩展到其他方面，逐渐地简化开发人员的工作，并且为框架增加更多灵活性。

所以，如果你计划创建自己的框架，或者在已有框架中引入代码生成，考虑一下这项技术的强大，以及...脆弱性。尝试生成简单的代码，并为所有的生成步骤和条件记录文档，因为任何 API 中的改动都会很容易破坏生成过程。
