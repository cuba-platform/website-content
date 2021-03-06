
如果你在一个村子远程办公、或者在航程8小时的飞机上、或者公司网络限制不能访问类似 maven central 这样的公共制件仓库，这时你该如何建立框架和依赖库的完整资源栈来保证舒适的开发过程呢？如何获取所有依赖的制件，包括传递依赖呢？又如何更新使用的框架版本？

我们来尝试解决这些问题。本文将介绍 CUBA SDK，这是一个命令行工具，可以解析所有 Maven 库的传递依赖，并且在远程仓库中管理这些依赖。然后，我们会展示一个例子，帮助你在任何使用 Maven 依赖的 Java 应用中使用我们这个最佳解决方案。

大家都知道，我们团队使用开源的 Java 框架 CUBA 平台来进行企业级开发。CUBA 平台有完整的生态，包括框架本身和提供各种额外功能的插件。这些扩展功能插件只需要点几下鼠标便可使用。经过几年的演进，框架越来越受欢迎。目前，全世界有超过两万的开发人员在使用。随着框架的进化，我们也遇到了许多有趣的案例。本文将详细的讲述其中一个案例。很有可能你的项目也需要类似的经验，尤其是你当你在一个对安全性要求非常严格的公司工作时。

## 加载传递依赖到内部仓库

几乎所有使用了 Apache Maven 或 Gradle 做为构建工具的 JVM 项目，它们使用的库或者框架都会有大量的传递依赖。由于在线共享仓库的存在，包依赖管理工具可以自动解决这些传递依赖。

但是如果你是在内网工作，无法访问公共仓库怎么办呢？
 
## 可选方案

办法可能显得简单 - 我们只需要使用 Nexus 来做公共仓库的代理。 确实，这个方案在大部分情况下是可行的，但是如果对安全性要求格外严格的话则行不通。一些特定类型公司或领域会遇到这类问题：军工、金融，政府各级部门等。在这类公司或部门工作，对访问公网是有严格约束的，并且一般都不准使用传统的代理。

所以我们怎么办？

- 方案 0. 求安全部门开个口子。
- 方案 1. 网关。
- 方案 2. 手工管理依赖。

方案 0 就别想了。我们来探讨一下方案 1 和 2。

网关选项，意味着一个公司有一台网关电脑，可以连接内网或外网。从外网检查并下载需要的依赖库，然后将依赖库加载到内部存储，之后，网关切到内网。这种情况下，为一个项目添加新的依赖或者更新依赖往往需要很多时间。因为你必须连上网关，下载必须的制件，确保所有传递依赖都能正确加载，然后将依赖检出到内部仓库。如果一个公司有许多项目，工作流程可能会出现等待，直到所有内部仓库有了所有需要的依赖。

再看看手工管理依赖，每次更新或者添加组件的时候，你都必须检查库依赖列表然后与已经载入的库一一比对。

我们可以看到，添加或者更新依赖都是相当复杂的过程。

即便不需要考虑企业的安全管理，访问在线仓库问题也会存在。比如，某个工程师即将在偏远的村子工作，那里网络很差。这种情况一般可以使用 Gradle/Maven 离线插件来处理。但如果你有好几个项目，对于每个项目，你都需要做这些事情：
- 配置一个离线插件
- 以防万一，尽量为项目添加所有可能用到的依赖，以保证离线插件可以缓存所有依赖
- 下载所有依赖到本地文件夹

这种方式看上去很不优雅，因为项目将会过度加载依赖库和配置，你得记住这些额外的依赖库和配置以便以后删除。尽管已经“以防万一”了，创建新项目还有可能出问题，因为还有可能缺少万一都没想到的依赖库。

那么我们能为解决这些问题做哪些工作？

## CUBA SDK

CUBA 平台的用户中，有一些公司由于安全的原因访问外网受限甚至禁止访问外网。

我们想要让这些用户的生活更加容易些，因此，我们设计了一个命令行工具 - CUBA SDK，它可以解析 CUBA 平台、扩展插件以及任何其他具有 maven 坐标的库的所有依赖。

那么 CUBA SDK 与 Gradle/Maven 离线插件有什么不同？

主要的不同在于 CUBA SDK 不是缓存特定项目依赖。而是提供了在内部仓库和外部仓库之间同步制件的能力，所以在一个封闭的环境的开发过程会因此变得流畅一些。
 
CUBA SDK 并不针对某个项目，所以才有可能从全局角度创建一个框架、扩展及库依赖的完整离线副本。

当开发多个项目时，或者在不知道需要哪些模块的情况下开启一个新项目时，开发人员会发现它非常有用。有了 SDK 的帮助，可以提前加载这些需要的模块到本地或者内部仓库。

同时，公司也可以使用 SDK 来做中央内部仓库的同步。

![text](http://192.168.45.58:1337/uploads/1ba02a67d60047cc97c6798d345289ae.png)

CUBA SDK 通过几个简单的命令，便可以为 CUBA 框架、扩展以及任何外部依赖库提供解析、导出以及上传所有依赖到外部仓库的能力。在完全孤立的网络下，你可以使用导入和导出命令或者在网关机器安装 CUBA SDK。

CUBA SDK 的优势：

- 自动检测源码和已加载库的所有依赖
- 为 CUBA 平台和插件解析依赖
- 检测新的库版本并安装
- 可以同时基于数个仓库搜索制件，包括本地 maven 仓库
- 自带 Nexus 开源仓库
- 同时上传制件到多个仓库，包括本地 maven 仓库
- 导入/导出制件以及所有依赖
- 安装 CUBA 平台或插件时提供带提示的交互模式
- 使用 Gradle 解析制件依赖。
- 不依赖 IDE
- 可以安装在 CI 服务器

## SDK 命令

完整的可用命令列表在 [GitHub](https://github.com/cuba-platform/cuba-sdk) 。

CUBA SDK 原生支持三种组件类型： CUBA 框架，CUBA 插件以及可以通过 maven 坐标加载的库。可以通过 CUBA SDK 插件来支持其他组件类型。

通过 **install** 命令可以安装一个组件到远程仓库。在开发 SDK 时，我们已经预见到 SDK 可能会被安装在一台网关机器或一个便携设备上。在这种情况下，可以通过 **resolve** 和 **push** 命令安装需要的组件。

**resolve** - 仅解析和下载所有依赖到你本地的 SDK 缓存。

**push** - 将加载的制件以及它们的依赖传到指定的目标仓库。

为了支持仓库相关的操作，SDK 内置一个仓库管理器。

仓库管理器支持本地和远程仓库，在 SDK 中将它们分为两组：

- **source** - 检索制件的仓库
- **target** - 载入制件的仓库

SDK 本身也可以做为一个仓库。 使用 **setup-nexus** 命令，SDK 会下载、安装并配置 Nexus OSS 仓库。启动、停止仓库的话使用 start 和 stop 命令。
 
检测、安装更新只需要运行 **check-updates** 命令。

## 解析依赖

SDK 计划解决的最主要的问题便是正确地为组件解决并收集依赖。在开发过程中，我们尝试了几种方案来解析组件的传递依赖。一开始我们的思路是解析 .pom 文件然后构造一个依赖树。但实际上，人工解析依赖并不是一个好办法，特别是 Apache Maven 还提供了开箱可用的解析功能。

## 使用 Maven 做依赖管理

所以，我们用 Apache Maven 作为工具来管理传递依赖。

基本原理是，CUBA SDK 加载 maven 的 **二进制分发** 到 SDK 主目录，然后通过 JRE 执行命令。

例如，下面这个命令
```
dependency:resolve -Dtransitive=true -DincludeParents=true -DoverWriteSnapshots=true -Dclassifier=<classifier> -f pom.xml
```

这个命令帮助我们解析 pom.xml 中描述的所有组件的传递依赖，然后这些组件会自动加载到本地 maven 缓存。之后，我们运行以下命令：

```
org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy-file -Durl=<repository URL>
```

来把制件载入指定的仓库。

下面的命令将库载入到本地仓库。

```
org.apache.maven.plugins:maven-dependency-plugin:3.1.1:get -Dartifact=<maven coordinates>
```
 
为了在 CUBA SDK 应用程序中运行 Maven 命令，我们生成了 settings.xml 文件。它包含拉取和载入制件需要的所有仓库。

## 使用 Gradle 做依赖管理
 
在第一版程序中，依赖可以正确的解析，但是很慢，并且在测试时，处理一些 CUBA 插件时存在冲突。不过，这些问题并没有出现在使用 Gradle 构建的项目中。

所以我们决定将依赖解析逻辑转向 Gradle。为此，我们创建了一个 build.gradle 脚本，包含一些载入和解析组件依赖而需要的任务。

为了调用 Gradle 任务我们使用了 Gradle Tooling API 。

要通过 Gradle 定义依赖路径，我们使用 “artifact resolution query API”。下面这段代码帮助我们拿到库源码的路径。

```
 def component = project.dependencies.createArtifactResolutionQuery()
            .forComponents(artifact.id.componentIdentifier)
            .withArtifacts(JvmLibrary, SourcesArtifact)
            .execute()
            .resolvedComponents[0]
 def sourceFile = component?.getArtifacts(SourcesArtifact)[0]?.file
```
 
这样，我们拿到了本地 Gradle 缓存所有文件的路径，然后把它们保存到 SDK 存储区域。

为了解析并载入组件的依赖到本地缓存，我们在配置中加入所有组件，然后通过 `lenientConfiguration` 拿到所有依赖。

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

同时，我们也用 `lenientConfiguration` 来避免 Gradle 脚本在找不到依赖的组件时会崩溃的问题。

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

多亏了 Gradle，我们避免了解析递依赖时的冲突，并大大加快了程序运行速度。

## 构建项目

对于构建 CUBA SDK ，我们使用了跟 CUBA CLI 同样的方案。通过 jlink 工具，我们把应用本身、所有必要的模块和一个定制的 JRE 打成一个包。这个方案可以使 SDK 不依赖外部安装的 Java 版本。[CLI Core Sample project](https://github.com/cuba-platform/cli-core-sample) 是一个这种类型应用的示例。


## 第三方插件支持

由于 CUBA SDK 是基于 [CLI Core library](https://github.com/cuba-platform/cli-core) 的，所以支持第三方插件。目前，SDK 支持 maven 和 gradle 组件依赖管理，以及通过第三方插件支持 CUBA 组件。

下面通过一个例子看如何通过插件扩展 SDK 的功能。我们创建一个对 Spring Boot Starters 的支持，使用著名的 [Spring Initializr](https://start.spring.io/) 进行项目初始化。

首先创建一个新项目。做为示例，我们使用 CUBA CLI（在 [这里](https://github.com/cuba-platform/cuba-cli/wiki/Plugin-Development) 有介绍），然后添加依赖：
```
implementation "com.haulmont.cli.core:cli-core:1.0.0"
implementation "com.haulmont.cli.sdk:cuba-sdk:1.0.1"
```

为 spring boot starters 创建一个新的 provider - SpringBootProvider，扩展了 BintraySearchComponentProvider。 BintraySearchComponentProvider 使用 
Bintray API 自动检索可访问组件版本。

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

 
这个 provider 会检索 spring-components.json 中可访问的组件。 spring-components.json 文件即 [Spring Initializr 应用中 yml文件的json版本](https://github.com/spring-io/start.spring.io/blob/5294926a91392c12cc709f481c3a1df9650d5747/start-site/src/main/resources/application.yml)。

 
为了将 json 映射为对象，我们来创建简单的数据类：

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

要将这个 provider 添加到 SDK providers 中，我们需要在插件的 init 事件中进行处理：

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

这就行了。现在，通过终端或者 IDE 安装这个插件，只需要运行 *gradle installPlugin* 命令。

运行 SDK
![text](http://192.168.45.58:1337/uploads/83754cff3c7440a7ad4ad3b5bb34572a.png)


我们可以看到插件已经成功加载。现在我们来用 *resolve boot-starter* 命令检查一下我们的逻辑操作：
![text](http://192.168.45.58:1337/uploads/5eac281fa5b342679ce4cbe129461f79.png)

![text](http://192.168.45.58:1337/uploads/5c09660756d0414591f2fa0a26ff2df8.png)


可以看到，组件和版本提示正确工作了。

如果你存储制件和依赖的方式与 maven 仓库不同，那么你可以用插件来实现你专属的 *ArtifactManager* 接口，它包含存储相关的逻辑。

测试插件的源码在 [GitHub page](https://github.com/cuba-platform/cuba-sdk-spring-boot-plugin) 。

## 总结
 
首先， CUBA SDK 对由于安全控制严格、对外网访问有限制的公司是非常有用的。

即使不考虑公司的安全策略，仓库的可用性对于开发人员也是重要的问题，例如，一个开发人员即将独自在乡下工作，网络连接很差。这种情况下，CUBA SDK 将是很棒的选择，它能帮助你在本地，在你个人电脑上，建立有效的库、框架栈。
