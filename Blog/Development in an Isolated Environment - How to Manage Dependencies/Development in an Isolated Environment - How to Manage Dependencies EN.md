How to build an actual stack of frameworks and libraries for a comfortable development process if you’re working remotely in a distant village, flying for 8 hours on a plane, or if the access to the world wide network in your company is so limited that you can’t use public artifact repositories, such as maven central? How can we deliver all the necessary artifacts we depend on, including transitive dependencies? How to update them with the new framework versions?

Let’s try to find answers to these questions. In this article I’ll tell you about CUBA SDK - a command line tool which provides an ability to resolve all transitive dependencies for Maven libraries and manage them in remote repositories. Also, we’ll overview the example which can help you use our best practice for any Java application using Maven dependencies.

As you know, our team is working on CUBA - Open Source Java framework for enterprise development. CUBA Platform is a whole ecosystem consisting of the framework and various add-ons that provide extra functionality. And this functionality is ready to use within several clicks.  For the last few years the framework gained huge popularity. Now it’s used by more than 20 000 developers worldwide. With the growth of popularity we met lots of interesting cases. This article will dwell on one of them. Probably, it could help with your project, especially if you work in a company which has a strict security system. 

## Loading Transitive Dependencies to Internal Repositories 

Almost any modern JVM project using Apache Maven or Gradle as a build tool has a big number of transitive dependencies for each of the i library and/or framework being used. With a shared repository accessible by the network, these dependencies are resolved automatically by a dependency manager. 

But what shall we do in case public repositories are inaccessible from the internal network? 

## Solution Options 

The solution may seem simple - we just take Nexus and proxy the access to repositories through it. Indeed, in most cases this will work, but not if you have extra strict security requirements. You can face them in companies of specific types: military, finance, ministries and other government authorities. Access to the internet in such companies is strictly regulated and usually does not allow traditional proxying. 

What shall we do? 

- Option 0. Begging the security team.
- Option 1. Gateway.
- Option 2. Manual dependency management.

Option 0 is not taken into account, let’s consider options 1 and 2. 

The gateway option implies that a company has a gateway computer which can be connected either to external or to internal network. After loading and checking the necessary libraries from the external network, the gateway switches to the internal network, and only after that the libraries are loaded to the internal repositories. Adding new dependencies or updating the existing ones for a single project usually takes a lot of time, as you must get access to the gateway, load the necessary artifacts, check that all transitive dependencies were loaded correctly, and then check out the dependencies to the internal repository. If there are many projects in the company, the working process may stand idle until the required dependencies become accessible from the internal repositories.

Considering the option with manual dependencies management, every time you update or add components you’ll have to check the library dependencies list and compare it with already loaded libraries. 

As we can see, adding or updating dependencies can be a rather complex process.

If you don’t consider corporate security systems, the repository access problem still exists. For example, a developer is going to work in the isolated countryside where the internet connection is weak. To prepare for such a case you can always try offline plugins for Gradle or Maven. But if you have several projects, for each of them you’ll have to 
- configure an offline plugin
- add to the project all potentially useful dependencies so that offline plugins could add them to the dependencies cache correctly, just in case 
- download all the dependencies to the local folder

Which is not quite comfortable, as the project could be overloaded with dependencies and configurations that you’ll have to keep in mind and delete from the project later. And even with all that we still have the problem with creating a new project cause it can lack the necessary modules. 

So what do we offer to solve those problems?

## CUBA SDK

Among CUBA Platform users there are companies which limit or restrict access to the external network for security reasons.

We decided to make the users’ life easier and make a command line tool CUBA SDK which could resolve all dependencies for CUBA Platform, add-ons and any other libraries which have maven coordinates. 

So what’s the difference between CUBA SDK and offline plugins for Gradle or Maven? 
The main difference is that CUBA SDK doesn’t cache the particular project dependencies. It allows you to synchronize artifacts between internal and external repositories, so that the development process in an isolated environment was comfortable.
CUBA SDK doesn’t require a project, so it is possible to create a required offline stack of frameworks, add-ons and libraries with all dependencies. 

Developers can find it useful if they work on several projects or plan a new one and therefore don’t know which modules will be used in the project. With the help of SDK these modules can be loaded to the local or internal repository in advance. 

The companies can profit from SDK by using it for the centralized internal repositories synchronization.

![text](http://192.168.45.58:1337/uploads/1ba02a67d60047cc97c6798d345289ae.png)

CUBA SDK provides an ability to resolve, export and upload to external repositories all dependencies for CUBA framework, add-ons or any external library with a few simple commands. For completely isolated networks you can use import and export commands or install CUBA SDK on the gateway.

CUBA SDK advantages:

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

## SDK Commands

The full list of available commands can be found on the [GitHub](https://github.com/cuba-platform/cuba-sdk).

CUBA SDK originally supports three component types: CUBA Framework, CUBA add-on and a library that can be loaded through maven coordinates. This list can be expanded for other component types  via CUBA SDK plugins. 

You can install a component to a remote repository via the **install** command. When creating SDK we have foreseen that SDK might be installed on a gateway machine or on a portable device. In this case the components installation is possible via **resolve** and **push** commands.

**resolve** - just resolves and downloads all dependencies to your local SDK cache
**push** - passes the loaded artifacts with their dependencies to the set target repositories

For working with repositories SDK has an embedded repository manager.

The repository manager supports local and remote repositories which are divided into two groups within SDK 
- **source** - repositories used for searching artifacts
- **target** - repositories to which the artifacts will be loaded

SDK itself can be used as a repository. Using the command **setup-nexus** SDK downloads, installs and configures Nexus OSS repository. For launching and stopping the repository use start and stop commands.

For checking and installing updates just run the command **check-updates**. 

## Resolving dependencies

The main problem that the SDK is intended to solve is the correct resolving and collection of dependencies for the components. During the development process we tried several approaches to resolving components’ transitive dependencies. Initially we had an idea that we can just parse the .pom files and compose a dependency tree. But actually, manual dependency parsing wasn’t a good idea, especially since Apache Maven can do it out-of-the-box. 

## Maven as Dependency Manager

So, we took Apache Maven as a tool for transitive dependencies management.

To achieve this CUBA SDK loads maven distributive to the SDK home folder and runs commands via Java Runtime.

For example, the command 
```
dependency:resolve -Dtransitive=true -DincludeParents=true -DoverWriteSnapshots=true -Dclassifier=<classifier> -f pom.xml
```

helped us to resolve all the components’ transitive dependencies described in pom.xml, and those components were automatically loaded to the local maven cash. After that we run the command
```
org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy-file -Durl=<repository URL>
```
which loaded the artifacts to the required repository.

The following command lets us load the library to the local repository.
```
org.apache.maven.plugins:maven-dependency-plugin:3.1.1:get -Dartifact=<maven coordinates>
```
For running Maven commands in the CUBA SDK application, we’ve generated the settings.xml file. It contains the list of all repositories that must be used for fetching and loading artifacts. 

## Gradle as Dependency Manager

In the first application version the dependencies resolved correctly but rather slowly, and while testing we got collisions when resolving dependencies for some CUBA Platform add-ons. However, there were no such problems during project build with Gradle. 

So we decided to switch the dependency resolving logic to Gradle. In order to do this we created a build.gradle script with the tasks required for loading and resolving the components dependencies.

For calling Gradle tasks we used Gradle Tooling API. 

To define the dependency path through Gradle we used artifact resolution query API. The following code helped us get the path to the library source:
```
 def component = project.dependencies.createArtifactResolutionQuery()
            .forComponents(artifact.id.componentIdentifier)
            .withArtifacts(JvmLibrary, SourcesArtifact)
            .execute()
            .resolvedComponents[0]
 def sourceFile = component?.getArtifacts(SourcesArtifact)[0]?.file
```
Thus, we got the paths to all files in the local Gradle cache and saved them to the SDK storage. 

To resolve and load dependencies for the components to the local cache we add the components to the configuration and get all dependencies using  `lenientConfiguration`. 
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

For loading artifacts to the repositories SDK runs the `PublishToMavenRepository` Gradle task.
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

## Project Build

For building CUBA SDK we used the same approach as for CUBA CLI. With the jlink tool we built all the necessary modules to bundle them with a  custom JRE delivered together with the application. This approach made SDK independent from the installed Java version. You can find an example of such a build in the [CLI Core Sample project](https://github.com/cuba-platform/cli-core-sample). 

## Third-party Plugins Support

Since CUBA SDK is based on the [CLI Core library](https://github.com/cuba-platform/cli-core), it supports third-party plugins. At the moment, SDK has maven and gradle components dependency managers and providers for CUBA components implemented via third-party plugins.

Let’s have a look at the example of how we can extend the SDK functionality with a plugin. We’ll create a provider for Spring Boot starters from the widely known [Spring Initializr](https://start.spring.io/).

First let’s create a new project. As an example we’ll use the CUBA CLI plugin, as it’s described [here](https://github.com/cuba-platform/cuba-cli/wiki/Plugin-Development), and add the dependencies:
```
implementation "com.haulmont.cli.core:cli-core:1.0.0"
implementation "com.haulmont.cli.sdk:cuba-sdk:1.0.1"
```
Create a new provider for spring boot starters - SpringBootProvider, which extends BintraySearchComponentProvider. BintraySearchComponentProvider enables automatic search of  accessible component versions using the Bintray API.
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

For mapping from  json to objects let’s create simple data classes: 
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

Run SDK
![text](http://192.168.45.58:1337/uploads/83754cff3c7440a7ad4ad3b5bb34572a.png)

We can see that our plugin was successfully loaded. Now let’s check that our logic operates with the help of *resolve boot-starter* command:
![text](http://192.168.45.58:1337/uploads/5eac281fa5b342679ce4cbe129461f79.png)

![text](http://192.168.45.58:1337/uploads/5c09660756d0414591f2fa0a26ff2df8.png)

As we can see, the prompts for components and their versions work as they should.

If you store artifacts and dependencies in the storage not the same way as in maven repositories, then you can use plugins to implement your own *ArtifactManager* interface, which can contain the logic for working with your storage.

The test plugin source code can be found on the [GitHub page](https://github.com/cuba-platform/cuba-sdk-spring-boot-plugin).

## Conclusion

First of all, CUBA SDK will be useful for companies which have limited access to the external network due to the security requirements. 

If we abstract from companies’ security policy, repositories availability problem is also important for developers, for example, if a developer is going to work alone in the countryside and has poor internet connection there. In this case CUBA SDK is a great choice that will help you to build up an efficient stack of libraries and frameworks locally, on your personal computer.