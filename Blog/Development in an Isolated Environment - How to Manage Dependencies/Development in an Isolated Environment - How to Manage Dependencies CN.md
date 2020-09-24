How to build an actual stack of frameworks and libraries for a comfortable development process if you’re working remotely in a distant village, flying for 8 hours on a plane, or if the access to the world wide network in your company is so limited that you can’t use public artifact repositories, such as maven central? How can we deliver all the necessary artifacts we depend on, including transitive dependencies? How to update them with the new framework versions?
如果你在一个村子远程办公，或者在航程8小时的飞机上，或者贵公司网络限制不能访问类似maven central的公共制件仓库，你怎么建立框架栈和依赖库来保证舒服的开发过程呢？ 怎么交付所有的依赖制件，以及传递依赖呢？如何更新到新的框架版本？

Let’s try to find answers to these questions. In this article I’ll tell you about CUBA SDK - a command line tool which provides an ability to resolve all transitive dependencies for Maven libraries and manage them in remote repositories. Also, we’ll overview the example which can help you use our best practice for any Java application using Maven dependencies.
我们来尝试解决这些问题。本文将向你介绍 CUBA SDK - 一个命令行工具，可以解决所有Maven库的传递依赖，并且在远程仓库中管理他们。并且，你将看到一个例子，帮助你在任何使用Maven依赖的Java应用中使用我们这个最佳解决方案。

As you know, our team is working on CUBA - Open Source Java framework for enterprise development. CUBA Platform is a whole ecosystem consisting of the framework and various add-ons that provide extra functionality. And this functionality is ready to use within several clicks.  For the last few years the framework gained huge popularity. Now it’s used by more than 20 000 developers worldwide. With the growth of popularity we met lots of interesting cases. This article will dwell on one of them. Probably, it could help with your project, especially if you work in a company which has a strict security system. 
你已经知道我们团队使用 CUBA - 开源Java框架 来进行企业级开发。CUBA平台是一个完整的生态，包括框架本身和提供额外功能的各式各样的插件。这些功能只需要几下点击便可使用。 过去几年框架越来越受欢迎。 目前，世界上有20000开发人员在使用它。 随着它的成长我们遇到了许多有趣的案例。本文将详细的讲述其中之一。 很大可能它会帮助到你的项目，尤其是你工作在一个具有严格的安全性管理系统的公司时。

## Loading Transitive Dependencies to Internal Repositories 
## 加载传递依赖到内部仓库

Almost any modern JVM project using Apache Maven or Gradle as a build tool has a big number of transitive dependencies for each of the i library and/or framework being used. With a shared repository accessible by the network, these dependencies are resolved automatically by a dependency manager. 
对于几乎任何使用了 Apache Maven 或 Gradle 做为构建工具的现代JVM项目，它们使用的 i 库或者框架都会有大量的传递依赖。由于共享、在线仓库的存在，这些依赖都可以被依赖管理工具自动解决。

But what shall we do in case public repositories are inaccessible from the internal network? 
但是如果所在的内部网络无法访问公共仓库怎么办呢？

## Solution Options 
## 可选方案

The solution may seem simple - we just take Nexus and proxy the access to repositories through it. Indeed, in most cases this will work, but not if you have extra strict security requirements. You can face them in companies of specific types: military, finance, ministries and other government authorities. Access to the internet in such companies is strictly regulated and usually does not allow traditional proxying. 
办法可能显得简单 - 我们只需要使用 Nexus 来做公共仓库的代理。 事实上，大部分情况下这个方案可行，但是安全性要求格外严格的话不行。一些特定类型公司会遇到这类问题：军事、金融，政府各级部门等。在这类公司访问公共网络是受到严格约束的，并且一般都不准使用传统的代理。

What shall we do? 
所以我们怎么办？

- Option 0. Begging the security team.
- Option 1. Gateway.
- Option 2. Manual dependency management.
- 方案 0. 乞求安全部门。
- 方案 1. 网关.
- 方案 2. 人工管理依赖。

Option 0 is not taken into account, let’s consider options 1 and 2. 
方案 0 就别想了。我们来探讨一下方案 1 和 2。

The gateway option implies that a company has a gateway computer which can be connected either to external or to internal network. After loading and checking the necessary libraries from the external network, the gateway switches to the internal network, and only after that the libraries are loaded to the internal repositories. Adding new dependencies or updating the existing ones for a single project usually takes a lot of time, as you must get access to the gateway, load the necessary artifacts, check that all transitive dependencies were loaded correctly, and then check out the dependencies to the internal repository. If there are many projects in the company, the working process may stand idle until the required dependencies become accessible from the internal repositories.
网关选项意味着一个公司有一台网关电脑，可以同时连接外网和内网。从外网检查并加载必须的库之后，网关切到内网，并且需要在所有库都载入到内部仓库之后。为单独项目添加新的依赖或者更新依赖往往需要许多时间，因为你必须连上网关，载入必须的制件，确保所有传递依赖都正确加载，然后将依赖检出到内部仓库。如果一个公司有许多项目，开发流程将会闲置，直到所有必须的依赖检出到内部仓库。

Considering the option with manual dependencies management, every time you update or add components you’ll have to check the library dependencies list and compare it with already loaded libraries. 
再考虑一下人工依赖管理，每次更新或者添加组件的时候，你都必须检查库依赖列表然后与已经载入的库一一比对。

As we can see, adding or updating dependencies can be a rather complex process.
能看出，添加或者更新依赖都是相当复杂的过程。

If you don’t consider corporate security systems, the repository access problem still exists. For example, a developer is going to work in the isolated countryside where the internet connection is weak. To prepare for such a case you can always try offline plugins for Gradle or Maven. But if you have several projects, for each of them you’ll have to 
- configure an offline plugin
- add to the project all potentially useful dependencies so that offline plugins could add them to the dependencies cache correctly, just in case 
- download all the dependencies to the local folder

如果不需要考虑安全管理系统，仓库访问问题也会存在。比如，某个工程师即将在偏远的网络很差的村子工作。 处理这类事情往往可以考虑 Gradle/Maven 离线插件。 但如果你有好几个项目，对于每个项目，你都需要：
- 配置一个离线插件
- 以防万一，尽量为项目添加潜在的有用的依赖，以便保证离线插件可以正确的把他们加到缓存
- 下载所有依赖到本地文件夹

Which is not quite comfortable, as the project could be overloaded with dependencies and configurations that you’ll have to keep in mind and delete from the project later. And even with all that we still have the problem with creating a new project cause it can lack the necessary modules. 
看上去一点都不舒坦，因为项目将会加载过多的依赖和配置，你得记住这些过多依赖和配置以便以后删除。 尽管如此，创建新项目还有可能出问题，因为还有可能缺少必要的模块。

So what do we offer to solve those problems?
所以我们能为解决这些问题提供什么？

## CUBA SDK
## CUBA SDK

Among CUBA Platform users there are companies which limit or restrict access to the external network for security reasons.
CUBA 平台的用户中，有一些公司由于安全的原因访问外网受限甚至禁止访问外网。

We decided to make the users’ life easier and make a command line tool CUBA SDK which could resolve all dependencies for CUBA Platform, add-ons and any other libraries which have maven coordinates. 
我们想要让用户的人生更加轻松，制作了一个命令行工具 CUBA SDK， 它可以解决所有 CUBA 平台的依赖，插件，以及任何其他带有 maven 坐标的库。

So what’s the difference between CUBA SDK and offline plugins for Gradle or Maven? 
那么 CUBA SDK 与 Gradle/Maven 离线插件有什么不同？

The main difference is that CUBA SDK doesn’t cache the particular project dependencies. It allows you to synchronize artifacts between internal and external repositories, so that the development process in an isolated environment was comfortable.
主要的不同在于 CUBA SDK 不缓存特别详尽的项目依赖。它可以让你在内部仓库和外部仓库之间同步制件，所以在一个隔绝的环境里开发过程也是舒服的。

CUBA SDK doesn’t require a project, so it is possible to create a required offline stack of frameworks, add-ons and libraries with all dependencies. 
CUBA SDK 不需要某个项目，所以才有可能创建一个必须的离线框架栈，插件以及所有依赖库。

Developers can find it useful if they work on several projects or plan a new one and therefore don’t know which modules will be used in the project. With the help of SDK these modules can be loaded to the local or internal repository in advance. 
当开发多个项目，或者开启一个新项目不知道需要哪些模块时，会发现它非常有用。由于 SDK 的帮助，这些模块可以被提前加载到本地或者内部仓库。

The companies can profit from SDK by using it for the centralized internal repositories synchronization.
公司也会受益于使用 SDK 来做中央内部仓库的同步。

![text](http://192.168.45.58:1337/uploads/1ba02a67d60047cc97c6798d345289ae.png)

CUBA SDK provides an ability to resolve, export and upload to external repositories all dependencies for CUBA framework, add-ons or any external library with a few simple commands. For completely isolated networks you can use import and export commands or install CUBA SDK on the gateway.
CUBA SDK 通过几个简单的命令，便可以为CUBA框架、插件以及任何外部库提供解决、导出以及上传所有依赖到外部仓库的能力。在完全孤立的网络下，你可以使用导入和导出命令或者在网关安装 CUBA SDK。

CUBA SDK advantages:
CUBA SDK 的优势：

- automatically collects all dependencies with the source code for the loaded libraries 
- resolves dependencies for CUBA Platform and add-ons
- checks for new libraries versions and installs them
- can work with several repositories at a time for artifacts search including local maven repositories
- has an embedded repository Nexus OSS 
- provides an ability to upload artifacts to several repositories at a time including local maven repositories 
- imports and exports artifacts with all dependencies
- provides an interactive mode with prompts for installing CUBA Platform and add-ons
- uses Gradle tools for resolving dependencies
- is IDE-Agnostic 
- can be installed on CI server
- 自动检测源码和已加载库的所有依赖
- 为 CUBA 平台和插件解决依赖
- 检测新版本并安装
- 可以同时基于数个仓库检索制件，包括本地maven仓库
- 自带嵌入的 Nexus OSS 仓库
- 同时上传制件到多个仓库，包括本地maven仓库
- 导入/导出制件以及所有依赖
- 安装CUBA平台或插件时提供带提示的交互模式
- 使用 Gradle 解决制件依赖。
- IDE不感知
- 可以安装在 CI 服务器

## SDK Commands
## SDK 命令

The full list of available commands can be found on the [GitHub](https://github.com/cuba-platform/cuba-sdk).
完整的可用命令可以在 [GitHub](https://github.com/cuba-platform/cuba-sdk) 找到。

CUBA SDK originally supports three component types: CUBA Framework, CUBA add-on and a library that can be loaded through maven coordinates. This list can be expanded for other component types  via CUBA SDK plugins. 
CUBA SDK 一开始支持三种组件类型： CUBA框架，CUBA插件以及可以通过maven坐标加载到的库。这个列表可以通过 CUBA SDK 插件扩展到其他组件类型。

You can install a component to a remote repository via the **install** command. When creating SDK we have foreseen that SDK might be installed on a gateway machine or on a portable device. In this case the components installation is possible via **resolve** and **push** commands.
你可以通过  **install** 命令安装一个组件到远程仓库。 当开发 SDK 的时候我们已经预见到 SDK 可能会安装在一台网关机器或一个编写设备上。在这种情况下，安装组件可以通过 **resolve** 和 **push** 命令。

**resolve** - just resolves and downloads all dependencies to your local SDK cache

**push** - passes the loaded artifacts with their dependencies to the set target repositories

**resolve** - 只是解决和下载所有依赖到你本地的 SDK 缓存

**push** - 将加载的制件以及它们的依赖传到指定的目标仓库

For working with repositories SDK has an embedded repository manager.
为了处理仓库 SDK 内置一个仓库管理器。

The repository manager supports local and remote repositories which are divided into two groups within SDK 
仓库管理器支持本地和远程仓库，SDK中将它们分为两组：

- **source** - repositories used for searching artifacts
- **target** - repositories to which the artifacts will be loaded
- **source** - 检索制件的仓库
- **target** - 载入制件的仓库

SDK itself can be used as a repository. Using the command **setup-nexus** SDK downloads, installs and configures Nexus OSS repository. For launching and stopping the repository use start and stop commands.
SDK 本身也可以做为一个仓库。 使用 **setup-nexus** 命令，SDK 会下载，安装，配置Nexus OSS仓库。启动、停止仓库的话使用 start 和 stop 命令。

For checking and installing updates just run the command **check-updates**. 
检测、安装更新只需要运行 **check-updates** 命令。

## Resolving dependencies
## 解决依赖

The main problem that the SDK is intended to solve is the correct resolving and collection of dependencies for the components. During the development process we tried several approaches to resolving components’ transitive dependencies. Initially we had an idea that we can just parse the .pom files and compose a dependency tree. But actually, manual dependency parsing wasn’t a good idea, especially since Apache Maven can do it out-of-the-box. 
SDK 打算解决的最主要的问题便是正确地为组件解决并收集依赖。在开发过程中，我们尝试了几种方案来解决组件的传递依赖。一开始我们的思路是解析 .pom 文件然后构造一个依赖树。 因为事实上，人工解析依赖不是一个好办法，尤其是 Apache Maven 并不能开箱支持。

## Maven as Dependency Manager
## 使用 Maven 做依赖管理

So, we took Apache Maven as a tool for transitive dependencies management.
所以，我们用 Apache Maven 做为一个传递依赖管理的工具。

To achieve this CUBA SDK loads maven distributive to the SDK home folder and runs commands via Java Runtime.
为了这个目的，CUBA SDK 加载 maven 发布到SDK主目录然后在Java运行时执行命令。

For example, the command 
例如，下面这个命令
```
dependency:resolve -Dtransitive=true -DincludeParents=true -DoverWriteSnapshots=true -Dclassifier=<classifier> -f pom.xml
```

helped us to resolve all the components’ transitive dependencies described in pom.xml, and those components were automatically loaded to the local maven cash. After that we run the command
帮助我们解决所有组件pom.xml中描述的传递依赖，这些组件会自动加载到本地maven缓存。 之后，我们运行以下命令：

```
org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy-file -Durl=<repository URL>
```
which loaded the artifacts to the required repository.
来把制件载入指定的仓库。

The following command lets us load the library to the local repository.
下面的命令将库载入到本地仓库。

```
org.apache.maven.plugins:maven-dependency-plugin:3.1.1:get -Dartifact=<maven coordinates>
```
For running Maven commands in the CUBA SDK application, we’ve generated the settings.xml file. It contains the list of all repositories that must be used for fetching and loading artifacts. 
对于在 CUBA SDK 应用中运行 Maven 命令，我们会生成 settings.xml 文件。它包含所有拉取和载入制件需要的仓库。

## Gradle as Dependency Manager
## 使用 Gradle 做依赖管理

In the first application version the dependencies resolved correctly but rather slowly, and while testing we got collisions when resolving dependencies for some CUBA Platform add-ons. However, there were no such problems during project build with Gradle. 
在第一版程序中，依赖可以正确的解决但是很慢，并且在测试时，处理一些CUBA插件时存在冲突。不过尽管如此，当使用Gradle构建项目时没有问题。

So we decided to switch the dependency resolving logic to Gradle. In order to do this we created a build.gradle script with the tasks required for loading and resolving the components dependencies.
所以我们决定将依赖解决逻辑转向Gradle。为了达到目的，我们创建了一个 build.gradle 脚本，包含一些载入和解决组件依赖而需要的任务。

For calling Gradle tasks we used Gradle Tooling API. 
为了调用 Gradle 任务我们使用了 Gradle Tooling API 。

To define the dependency path through Gradle we used artifact resolution query API. The following code helped us get the path to the library source:
为了定义Gradle依赖路径我们使用了 artifact resolution query API 。下面这段代码帮助我们拿到库源码的路径。

```
 def component = project.dependencies.createArtifactResolutionQuery()
            .forComponents(artifact.id.componentIdentifier)
            .withArtifacts(JvmLibrary, SourcesArtifact)
            .execute()
            .resolvedComponents[0]
 def sourceFile = component?.getArtifacts(SourcesArtifact)[0]?.file
```
Thus, we got the paths to all files in the local Gradle cache and saved them to the SDK storage. 
这样，我们拿到了本地Gradle缓存所有文件的路径，然后把它们保存到SDK存储区域。

To resolve and load dependencies for the components to the local cache we add the components to the configuration and get all dependencies using  
`lenientConfiguration`. 
要解决并载入组件的依赖到本地婚车，我们把组件加入配置然后通过 `lenientConfiguration` 拿到所有依赖。

```

project.ext.properties["toResolve"].tokenize(';').each {
            dependencies.add 'extraLibs', it
        }
        def resolved = [:]
        configurations.all.collect {
            if (it.canBeResolved) {
                it.resolvedConfiguration.lenientConfiguration.artifacts.each { art ->
                    try {
                        ...
                    } catch (e) {
                        logger.error("Error: " + e.getMessage(), e)
                        logger.error("could not find pom for {}", art.file)
                    }
                }
            }
        }
```        
We use  `lenientConfiguration` to prevent Gradle script from crashing in case the component can’t be found in the repositories.
我们使用 `lenientConfiguration` 来避免Gradle脚本找不到组件时crash。

For loading artifacts to the repositories SDK runs the `PublishToMavenRepository` Gradle task.
为了将制件载入仓库，SDK 使用了 Gradle 任务 `PublishToMavenRepository` 。
```
task publishArtifact(type: PublishToMavenRepository) {
    doLast {
        if (project.ext.hasProperty("toUpload")) {
            def toUpload = new JsonSlurper().parseText(project.ext.properties["toUpload"])
            def descriptors = new JsonSlurper().parseText(project.ext.properties["descriptors"])

            artifactId toUpload.artifactId
            groupId toUpload.groupId
            version toUpload.version
            descriptors.each { descriptor ->
                artifact(descriptor.filePath) {
                    classifier descriptor.classifier.type
                    extension descriptor.classifier.extenstion
                }
            }
        }
    }
}
```
Thanks to  Gradle we avoid the collisions while resolving transitive dependencies and significantly speed up the application operation. 
感谢 Gradle，我们避免了解决传递依赖时的冲突，并大大加快了程序运行速度。

## Project Build
## 构建项目

For building CUBA SDK we used the same approach as for CUBA CLI. With the jlink tool we built all the necessary modules to bundle them with a  custom JRE delivered together with the application. This approach made SDK independent from the installed Java version. You can find an example of such a build in the [CLI Core Sample project](https://github.com/cuba-platform/cli-core-sample). 
要构建 CUBA SDK 我们使用了跟 CUBA CLI 同样的方案。通过 jlink 工具，我们把应用本身、所有必要的模块和一个定制的JRE打成一个包。这个方案可以使 SDK 不依赖安装的 Java 版本。你可以在 [CLI Core Sample project](https://github.com/cuba-platform/cli-core-sample) 找到一个这类应用的例子。

## Third-party Plugins Support
## 第三方插件支持

Since CUBA SDK is based on the [CLI Core library](https://github.com/cuba-platform/cli-core), it supports third-party plugins. At the moment, SDK has maven and gradle components dependency managers and providers for CUBA components implemented via third-party plugins.
既然 CUBA SDK 是基于 [CLI Core library](https://github.com/cuba-platform/cli-core)的，那么它支持第三方插件。目前，SDK 支持 maven 和 gradle 组件依赖管理，以及通过第三方插件支持 CUBA 组件。

Let’s have a look at the example of how we can extend the SDK functionality with a plugin. We’ll create a provider for Spring Boot starters from the widely known [Spring Initializr](https://start.spring.io/).
我们来通过一个例子看如何通过插件扩展 SDK 的功能。我们创建一个对来自众所周知的 [Spring Initializr](https://start.spring.io/) 的 spring boot starters 的支持。

First let’s create a new project. As an example we’ll use the CUBA CLI plugin, as it’s described [here](https://github.com/cuba-platform/cuba-cli/wiki/Plugin-Development), and add the dependencies:
首先创建一个新项目。 做为示例，我们使用 CUBA CLI 插件，它在 [这里](https://github.com/cuba-platform/cuba-cli/wiki/Plugin-Development) 有介绍，然后添加依赖：
```
implementation "com.haulmont.cli.core:cli-core:1.0.0"
implementation "com.haulmont.cli.sdk:cuba-sdk:1.0.1"
```
Create a new provider for spring boot starters - SpringBootProvider, which extends BintraySearchComponentProvider. BintraySearchComponentProvider enables automatic search of  accessible component versions using the Bintray API.
为 spring boot starters 创建一个新的provider - SpringBootProvider， 它扩展了 BintraySearchComponentProvider。 BintraySearchComponentProvider 使用 Bintray API 开启了自动检索可访问的组件版本的功能。

```
class SpringBootProvider : BintraySearchComponentProvider() {
   var springComponentsInfo: SpringComponentsInfo? = null

   override fun getType() = "boot-starter"
   override fun getName() = "Spring boot starter"

 ...

   override fun load() {
       springComponentsInfo = Gson().fromJson(readSpringFile(), SpringComponentsInfo::class.java)
   }

   private fun readSpringFile(): String {
       return SpringComponentsPlugin::class.java.getResourceAsStream("spring-components.json")
           .bufferedReader()
           .use { it.readText() }
   }
```

This provider will search for the accessible components from spring-components.json file which is the [json version of yml file in Spring Initializr application](https://github.com/spring-io/start.spring.io/blob/5294926a91392c12cc709f481c3a1df9650d5747/start-site/src/main/resources/application.yml). 
这个 provider 会检索 spring-components.json 中可访问的组件。 spring-components.json 文件即 [Spring Initializr 应用中 yml文件的json版本](https://github.com/spring-io/start.spring.io/blob/5294926a91392c12cc709f481c3a1df9650d5747/start-site/src/main/resources/application.yml)。

For mapping from  json to objects let’s create simple data classes: 
为了将json映射到对象，我们来创建简单的数据类：

```
data class SpringComponent(
   val name: String,
   val id: String,
   val groupId: String?,
   val artifactId: String?,
   val description: String?,
   val starter: Boolean? = true
)

data class SpringComponentCategory(
   val name: String,
   val content: List<SpringComponent>
)

data class SpringInitializr(
   val dependencies: List<SpringComponentCategory>
)

data class SpringComponentsInfo(
   val initializr: SpringInitializr
)
```
To add this provider to other SDK providers we need to register the provider in the init event of the plugin:
为了将这个provider添加到其他的SDK providers中，我们需要在插件 init 事件中注册它：

```
class SpringBootComponentsPlugin : CliPlugin {
   private val componentRegistry: ComponentRegistry by sdkKodein.instance<ComponentRegistry>()

   @Subscribe
   fun onInit(event: InitPluginEvent) {
       val bootProvider = SpringBootProvider()
       componentRegistry.addProviders(bootProvider)
       bootProvider.load()
   }

}
```

And that’s it. Now, to install the plugin through the terminal or IDE, run the *gradle installPlugin* command.
这就好了。现在，在终端或者IDE中安装这个插件，只需要运行 *gradle installPlugin* 命令。

Run SDK
运行 SDK

![text](http://192.168.45.58:1337/uploads/83754cff3c7440a7ad4ad3b5bb34572a.png)

We can see that our plugin was successfully loaded. Now let’s check that our logic operates with the help of *resolve boot-starter* command:
我们看到我们的插件已经成功加载。现在我们来用 *resolve boot-starter* 命令检查一下我们的逻辑操作：
![text](http://192.168.45.58:1337/uploads/5eac281fa5b342679ce4cbe129461f79.png)

![text](http://192.168.45.58:1337/uploads/5c09660756d0414591f2fa0a26ff2df8.png)

As we can see, the prompts for components and their versions work as they should.
我们能看到，组件的提示和版本正确工作了。

If you store artifacts and dependencies in the storage not the same way as in maven repositories, then you can use plugins to implement your own *ArtifactManager* interface, which can contain the logic for working with your storage.
如果你存储制件和依赖的方式与 maven 仓库不同，那么你可以用插件来实现你专属的 *ArtifactManager* 接口，它包含存储相关的逻辑。

The test plugin source code can be found on the [GitHub page](https://github.com/cuba-platform/cuba-sdk-spring-boot-plugin).
测试的插件源码在 [GitHub page](https://github.com/cuba-platform/cuba-sdk-spring-boot-plugin) 可以找到。

## Conclusion
## 总结

First of all, CUBA SDK will be useful for companies which have limited access to the external network due to the security requirements. 
首先， CUBA SDK 对由于安全需要，对外网访问有限的的公司是非常有用的。

If we abstract from companies’ security policy, repositories availability problem is also important for developers, for example, if a developer is going to work alone in the countryside and has poor internet connection there. In this case CUBA SDK is a great choice that will help you to build up an efficient stack of libraries and frameworks locally, on your personal computer.
即使没有公司的安全策略，仓库可用性对于开发人员也是重要的问题，例如，一个开发人员即将独自在乡下工作，网络连接很差。 这种情况下，CUBA SDK 将是很棒的选择，它能帮助你在本地，在你个人电脑上，建立有效的库、框架栈。