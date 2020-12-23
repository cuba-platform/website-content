## 概述

Jmix 是 CUBA 平台的新名称，也是一个重要的发行版。它目前处于预览阶段，我们计划 2021 年二季度发布稳定版。
主要特性：
- 基于 Spring Boot
- 分解为独立的模块(数据、安全、审计等)
- 新的数据模型定义方式
- 基于 Liquibase 的 DB 更新机制
- 充分利用 Spring Boot 的部署功能，更好地与云环境集成


将来的 UI 开发主要基于  ReactJS 框架，但是我们会保留基于 Vaadin 的 UI 作为备选方案。 

CUBA 平台会得到很长时间的支持，同时我们也通过兼容 API 的方式提供 CUBA 到 Jmix 的迁移路径。


从这里 [jmix.io](https://www.jmix.io/) 获取 Jmix。在专门的[论坛分类](https://forum.cuba-platform.cn/discuss/c/jmix) 讨论 Jmix 。

<a href="http://jmix.io" class="button">获取 jmix.io</a> <a href="https://www.cuba-platform.com/discuss/c/jmix" class="button forum" style="background: #6c5ce7!important; margin-left:30px;">Leave your feedback</a>

 
## 介绍

CUBA 始于 2008 年，此后经历了几个非常重要的阶段。最初，它是一个内部框架，文档匮乏、只提供很少的 API。只用于 Haulmont 公司内部，使公司能够更快地开发业务应用程序。

2015 年，CUBA 使用一个专有许可在世界范围内发布。那年我们只获得了很少几个用户 - 这相当尴尬。很明显，许可策略应该切换到开源。

2016 - 2017年是非常富有成效的两年，我们获得了广泛的社区支持。这是一个很大的转变，我们找到正确的方法。

在 2018 - 2019 年，我们开始引入一个清晰且文档全面的 API 级别，同时将 CUBA Studio 迁移到 IntelliJ。所有这些带来了更大的社区和更多的反馈。现在，我们处在下一个重要变化的起点。让我们深入了解一下 2021 年会发生什么。


## 新版本目标

我们计划在 CUBA 平台的下一个版本中达到以下目标：
1. 使开发人员的体验更接近当下流行的框架。CUBA 平台使用 Spring，但如今 Spring Boot 几乎征服了整个世界。新的框架正在出现 - Micronaut 、Quarkus。它们都具有相似的核心理念：通过 .properties 或.yaml 简化配置、广泛使用注解以及简单的插件接入和配置。我们希望 CUBA 为开发人员提供类似的体验。

2. 不重复发明轮子。自 2008 年以来，已经发展出了许多新的库和工具，现在它们已经很成熟，可以用于企业级应用程序。因此，我们想用经过实践检验的库替换一些自定义 CUBA 的模块，比如数据库迁移系统。   

3. 给 CUBA 应用程序瘦身。使用 CUBA 创建应用程序时，会引入一些不必要的功能，比如数据审计功能，但是它是核心框架的组成部分，我们不能将其排除。这会给数据库带来不必要的表、在服务器上启动额外的服务。因此，能按需加载一些功能会更好。   

4. 另外一个最重要的目标 - 舒适的开发体验和开发速度。

我们要聊的第一件事件...

## 名称

“CUBA 代表什么?” - 这个问题已经被问了无数次。坦白地说，这只是在 2008年我们随意选择的一个包名，因为它不长也不短。如果你深入探索一下 CUBA 源码 , 你也会发现类似的包名：“chile” 和 “bali” 。

在 2021 年我们要发布一个新重大版本，框架的名称将会改变。从 "CUBA" 变为 “Jmix”。 这个名称解释起来更简单： "J" 表示  "Java"  ,"mix" 表示多种技术和框架混合在一个应用程序。这样就少了很多问题，不会再与著名的岛屿名、国家名（古巴）和中国男子篮球混淆。

实际上，Jmix 是保留了熟悉的 API 和开发方法的下一个主要 CUBA 版本。它仍然是同一套便捷的工具和代码生成器。

但是重命名是很重要的一件事情，它也表示其中包含了重要的变化...


## 基础架构

从广义上讲，在 CUBA 中我们重复了一些 Spring Boot 做的事情 。比如我们自己的会话存储、安全子系统、身份验证……以及部署。Also, CUBA add-ons were introduced as a response to Boot starters with their own encapsulation and autoconfiguration mechanism.

当我们在 2008 年开始进行 CUBA 开发时，我们使用 “纯”  Spring 作为基础技术。在 Jmix 中，我们将使用 Spring Boot 作为我们的基础技术。

使用 Spring Boot 会带来以下好下：


1. 开发人员经验。 现在几乎每个 Java  开发者都熟悉 Spring Boot 。Spring Boot 的开发经验可以完全用在 Jmix 上，不需要学习新的框架，仅需要了解一些 Starter。  
2. 关于 Starter，基于 Spring Boot 的基础技术允许我们在我们的框架上使用所有现有的 Starter 。所以我们可以利用大量的社区资源。
3. 还有一点 - Spring Boot 在部署方面非常棒的功能，包括开箱即用的容器化支持。

在提到 Spring Boot Starter 时，我们必须说说 CUBA 扩展。我们来看看...

![text]({{strapiUrl}}/uploads/c1a763a2f95443c1821b4275b7e4eea3.png)

## 模块化

很多次，有开发人员向我们抱怨，只创建一个空 CUBA 应用程序，没有一行业务逻辑代码，即使这样，也会应用程序也会创建大量表，并且带有一些从来不会用的功能。

在 CUBA 7中，我们开始将一些核心功能提取为独立的扩展。基本上，CUBA 是一个 API 集合，这种方式为模块化处理提供了足够的灵活性。

从 Jmix 开始，您可以独立地使用框架功能，比如安全、审计等。现在所有的功能都是以 Spring Boot Starter 的形式提供。比如 CUBA 中的数据审计功能，在 Jmix 中是一个独立的模块。这个模块又被分为 Core 和 UI 模块。这意味着您可以使用整个模块作为数据审计引擎，也可以只使用 Core 模块，然后用自己实现的 UI 代替内置的。

Some functionality like healthcheck is replaced with Spring Boot actuator (see “Do not reinvent the wheel” section).

一些功能使用 Spring Boot 提供的功能代替了，比如 healthcheck 使用 Spring Boot actuator 替换 (见 “不重复造轮子”  章节)。

Jmxix 提供了超过 20 个 Starter 。下面是一些 Starter 和依赖关系：

<table>
<tbody>
<tr>
<td>
<p><strong>功能</strong></p>
</td>
<td>
<p><strong>Starter</strong></p>
</td>
<td>
<p><strong>依赖</strong></p>
</td>
</tr>
<tr>
<td>
<p>Entity log</p>
</td>
<td>
<p>Audit</p>
</td>
<td>
<p>Data</p>
</td>
</tr>
<tr>
<td>
<p>File storage</p>
</td>
<td>
<p>Core</p>
</td>
<td>&nbsp;</td>
</tr>
<tr>
<td>
<p>User settings</p>
</td>
<td>
<p>UI Persistence</p>
</td>
<td>
<p>Data</p>
</td>
</tr>
<tr>
<td>
<p>Table presentations</p>
</td>
<td>
<p>UI Persistence</p>
</td>
<td>
<p>Data</p>
</td>
</tr>
<tr>
<td>
<p>Entity log</p>
</td>
<td>
<p>Audit UI</p>
</td>
<td>
<p>Audit, UI</p>
</td>
</tr>
<tr>
<td>
<p>Config properties stored in DB</p>
</td>
<td>
<p>Core</p>
</td>
<td>&nbsp;</td>
</tr>
<tr>
<td>
<p>Restore deleted entities</p>
</td>
<td>
<p>Data tools</p>
</td>
<td>
<p>Data, UI</p>
</td>
</tr>
<tr>
<td>
<p>User sessions</p>
</td>
<td>
<p>Core</p>
</td>
<td>&nbsp;</td>
</tr>
<tr>
<td>
<p>Dynamic attributes</p>
</td>
<td>
<p>Dynamic attributes</p>
</td>
<td>
<p>Data, UI</p>
</td>
</tr>
<tr>
<td>
<p>Entity Snapshots</p>
</td>
<td>
<p>Audit</p>
</td>
<td>
<p>Data</p>
</td>
</tr>
<tr>
<td>
<p>Related entities</p>
</td>
<td>
<p>Advanced Data Operations</p>
</td>
<td>
<p>Data, UI</p>
</td>
</tr>
<tr>
<td>
<p>Bulk editor</p>
</td>
<td>
<p>Advanced Data Operations</p>
</td>
<td>
<p>Data, UI</p>
</td>
</tr>
</tbody>
</table>


As you can see, “data” starter is used by almost all Jmix modules. And it is not a surprise, because one of the strongest sides of the CUBA Platform was its...



## 数据访问层

We’re going to introduce a lot of changes here, keeping all the best parts of CUBA and introducing new features that will help you to be more productive.

With all the advantages, CUBA’s data model has one fundamental flaw: it is pretty rigid. For example, if you needed soft delete, you had to implement a proper interface (or inherit your entity from `BaseEntity`) and introduce columns with predefined names in the corresponding table. All primary keys had to be stored in the column with the `id` name if you used the `StandardEntity` class. 

This led to limitations during new data model development. And it also caused a lot of issues when developers tried to implement applications with CUBA using an existing data model. This was a case when CUBA was used as a modern framework to migrate from legacy or unsupported frameworks.

In Jmix, the Entity model becomes more flexible. You don’t need to extend the StandardEntity class or implement the Entity interface anymore. Just add the annotation `@JmixEntity` to a class to make it accessible to the framework. 

We decided to deprecate interfaces that specify an entity’s functionality. They are replaced with common annotations. For example, we use JPA’s `@Version` annotation and Spring Boot’s `@CreatedBy` instead of proprietary `Versioned` and `Creatable` interfaces. 

This allows us to make code more explicit - you can tell which features are supported by the entity by just looking at its code. 


```
@JmixEntity
@Table(name = "CONTACT")
@Entity(name = "Contact")
public class Contact {

   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   @Column(name = "ID", nullable = false)
   private Long id;

   @Version
   @Column(name = "VERSION", nullable = false)
   private Integer version;

   @InstanceName
   @NotNull
   @Column(name = "NAME", nullable = false, unique = true)
   private String name;

   @LastModifiedBy
   @Column(name = "LAST_MODIFIED_BY")
   private String lastModifiedBy;

   @Temporal(TemporalType.TIMESTAMP)
   @LastModifiedDate
   @Column(name = "LAST_MODIFIED_DATE")
   private Date lastModifiedDate;

```

“CUBA Views” are now “Fetch Plans”. The new name describes the purpose of these artifacts much better. 

Jmix’s data access layer now supports automatic lazy loading of references. So if you choose not to use fetch plans for filtering out local attributes, you will never get the notorious “UnfetchedAttributeException” anymore.

Another big thing is the database generation and update process. We decided to use Liquibase instead of the custom DB update engine. Spring Boot is responsible for running these scripts. This is another example of “not reinventing the wheel” - using a well-known engine with good documentation. 

Liquibase can generate DB-agnostic update scripts, so if you develop a product or an add-on with Jmix, you won’t need to generate database creation scripts for all possible RDBMSes. Liquibase will use the appropriate SQL dialect depending on a JDBC driver. At the same time, it allows DB-specific SQL customization, if you need it. 

And we keep the good old CUBA magic. Whenever you change an entity, Jmix Studio will generate a Liquibase script entry to reflect the changes.

What else can we think about when we talk about CUBA? Of course, it is the framework’s advanced... 

## 安全

In Jmix security is a separate module. Now you can choose whether or not you want the CUBA security engine or something else. 

And we reworked our security engine, now it is tightly integrated with spring-security. In order to simplify things for developers and “do not reinvent the wheel” concept, we’ve replaced our custom engine with the “standard” framework that has a lot of documentation and is familiar to developers. The model has changed accordingly - in Jmix we use classes from Spring Security like UserDetails and SessionRegistry, so it might look a bit unfamiliar at the beginning. 

Roles have changed too - we have merged Roles and Access Groups to simplify security management. In addition to this, we’ve added an “Aggregation Role”. This is a role that is composed from other roles - there were a lot of requests from users about this feature. 

Spring security is not the easiest thing to set up but in Jmix we made this process as seamless as possible. You will be able to set access to entities, attributes, screens, as well as set up row-based security as it was in CUBA.

And as it was in CUBA, all you need to do to install the security into your application is to add a dependency to your project and Jmix will do its magic - you will have security set up as well as user management UI (if you want to).

Apart from security, there is one more thing that everyone liked about CUBA: the simplicity of the development of an application’s...

## 用户界面

Backoffice UI (a.k.a. Generic UI) stays intact. Component-based UI was a big part of CUBA and we’re planning to support it in Jmix. Support includes screen generators, UI editors in the IDE, etc. We have added new components like a separate Pagination component or ResponsiveGridLayout. The only difference - now you can exclude Generic UI from the application completely thanks to the modular structure of the Jmix framework. 

Please note that Jmix applications are single-tiered; there is no more “core-web” separation. This approach is simpler and fits into the modern architectural approach. If you want a separation, you can achieve it by implementing two Jmix applications and create a REST API for communication. In this case, UI won’t depend on the data model and you will pass DTOs in order to display data on the front-end or process it on the back-end.

When we were planning the new version of our framework, we could not ignore the rise of JS UI frameworks. So we started developing ReactJS client generators (front-end module) in CUBA 7 and we continue this work in Jmix. JS frameworks usually have a pretty steep learning curve, so our aim is to make the ReactJS development experience with Jmix closer to Generic UI development. 

We have introduced TypeScript SDK and developed a set of custom ReactJS components that can be used for the UI development. The front-end module relies on the Generic REST API which is familiar for CUBA developers. The IDE supports basic UI generation with ReactJS. 

For now a developer can generate a familiar “browser” and “editor” UI screens using our components. Later we plan to add more components and simplify ReactJS UI development in the Studio. 

And now we have discussed almost all major changes in Jmix, but it would be unfair not to mention...

![text]({{strapiUrl}}/uploads/9b0dd8c1ff404e01aa0d9e90b1729b2e.png)

## 部署

CUBA has two deploy formats: WAR and UberJar and two options: a single app deployment or a separate applications deployment (core+web+...). 
 
Jmix will use Spring Boot build plugins for application deploy. It means that you can run a Jmix app as an executable fat JAR or deployable WAR (it also can be executed as a standalone application). 

But the best thing here is containerization. Now you don’t need to create your own Docker file to create an application image, its generation is supported out-of-the-box. Also, you can create layered JARs to make Docker images more efficient. In addition to this, cloud native buildpacks are supported. 

So, Jmix applications will use the cutting edge technologies for deployment in modern cloud environments.

And with so many changes, how about...

## 从 CUBA 迁移到 Jmix

First thing: we are not going to abandon CUBA. Version 7.3 is going to be long-term-support and will be supported for five years. And after that, there will be commercial support available for the next five years. So, CUBA framework will live for at least next 10 years.

At the moment Jmix is at Preview stage, so we’re going to stabilize it for some time before announcing production-ready stable release - provisionally in Q2 2021. But if you plan to start using CUBA, have a look at Jmix first. It is stable enough to start PoC development. And remember that almost all Spring Boot ecosystem is at your service. 

For backward compatibility purposes, we have introduced a jmix-cuba module. This module contains most of the APIs implemented in CUBA. So, you won’t need to change your code much in order to migrate to the next framework version. The compatibility module will be added automatically to your application during migration.

Most CUBA add-ons will be gradually migrated to Jmix platform: reporting, maps, business processes. As we did with CUBA 7 before, we might deprecate some modules when moving to Jmix because there is a Spring Boot starter with the same functionality.

The add-ons format has changed, but functionality stays the same. You can extend entities and screens using the same technique that was used in CUBA. As for services override, you’ll need to use Spring Boot approach - just mark the add-on service with the Primary annotation and functionality will be replaced. 

As usual, when the major version is introduced (basically, Jmix is CUBA 8), some breaking changes may appear. And those changes, as well as workarounds, will be described in details in the documentation. 

And the big part of the migration is the new Studio. Jmix Studio plays a very important role in the framework ecosystem. So, you still be able to use all familiar designers (entity and UI), shortcuts and intentions. And Jmix Studio will help you with creating applications using the framework. 

## 总结

Jmix is the next big step in CUBA Platform evolution. Now you can enjoy almost all Spring Boot starters and use techniques applicable for the most popular Java framework in the world. At the same time you still have all the convenient and familiar APIs and functionality provided by Jmix - a CUBA descendant as well as great development experience with the new Studio.  

<a href="http://jmix.io" class="button">Check out jmix.io</a> <a href="https://www.cuba-platform.com/discuss/c/jmix" class="button forum" style="background: #6c5ce7!important; margin-left:30px;">Leave your feedback</a>
<style>.forum:hover, .forum:focus {box-shadow: 0 5px 20px 0 #6c5ce7;}</style>