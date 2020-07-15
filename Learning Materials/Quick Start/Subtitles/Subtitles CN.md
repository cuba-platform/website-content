## Quick Start
## 快速开始 

Hi! Welcome to the CUBA Platform Quick Start Video.
大家好！欢迎观看《CUBA 平台快速开始》视频介绍。
  
CUBA Platform is an open source framework based on the well-known Spring framework.
CUBA 平台是一个基于 Spring 开发的开源框架。
  
The purpose of CUBA is to streamline the development process of business applications.
CUBA 的目标是将业务应用系统的开发过程进行流程化。
  
This quick start video covers the following topics:
本集快速开始视频包含以下主题：

* Data model and database creation
* User interface development and customization
* And business logic implementation
* 数据模型和创建数据库
* 用户界面开发与定制
* 业务逻辑实现

As an example, we will create a simple, but fully functional version of an application that will help with session planning for a conference. 
作为示例，我们会开发一个简单但是功能完备的应用程序；该程序可以用来为大会做会议计划。

The video will be just enough for you to start developing with CUBA.
观看该视频足以使您能开始使用 CUBA 进行开发。

For this video, in addition to the CUBA Platform framework we will use a plugin for IntelliJ Idea - CUBA Studio.
在该视频中，除了使用 CUBA 平台框架之外，我们还会使用 IntelliJ Idea 的一个插件 - CUBA Studio。

## Project Creation
## 创建项目

Let’s create an empty CUBA project with the project namespace - planner. We will use Java 11 as the default JDK and the latest stable CUBA version.
我们来创建一个空的 CUBA 项目，它的命名空间为：planner。我们将使用 Java 11 作为默认 JDK，并基于最新的CUBA稳定版。

We will use an in-memory database - HSQLDB. This should be enough for the quick start. You can select another database from the drop-down list. Leave database connection parameters unchanged.
我们将使用一个内存数据库 - HSQLDB。对于快速开始的话足够了。你也可以从下拉列表中选择其他数据库，先不要修改数据库的连接参数。

The project name will be Session Planner.
项目的名称为 Session Planner。

After the first launch CUBA Studio offers you to apply a trial subscription to CUBA Studio Premium. It is free for 28 days and gives us some useful optional designers. Let’s apply it.
第一次启动CUBA Studio的时候，它会提示你申请一个CUBA Studio的商业试用版，免费试用28天，提供一些非常有用的可选的设计器，我们来开始使用这个商业试用版。

On the left part of the screen you can see the CUBA Project navigation tree. In the center there is a Welcome page, you can use it for quick access to some CUBA functions, documentation, marketplace, and also versions and subscription information.
界面的左侧是CUBA项目的导航树。中间是一个欢迎页面，你可以通过它快速打开一些CUBA功能、文档、应用市场，也能看到版本和订阅信息。

On the right side of the screen you can see the Gradle Tool window. CUBA framework uses Gradle to build and run the application.
界面的右侧是Gradle工具窗口。CUBA 框架使用 Gradle 作为构建工具。

The standard IntelliJ’s build window is at the bottom. We can close some windows to get some space on the screen. The CUBA main menu provides quick access to the CUBA-specific features of the IDE.
标准的IntelliJ的构建窗口在下方。我们可以关闭一些窗口节省屏幕空间。CUBA主菜单提供了CUBA相关的IDE功能入口。

## Data Model Creation
## 创建数据模型

Now we will create JPA classes along with business rules like mandatory fields, unique fields and relations.
现在我们要创建带有业务规则的 JPA 类，比如必要字段、唯一字段和关系。
  
The business domain model has only two classes (also called entities): Speaker and Session.
业务领域模型只有两个类（也称作实体）：Speaker 和 Session（发言人和会议）。

To simplify the development process we added a visual designer to CUBA Studio that makes JPA entities creation easier. Right click on the “Data Model” and select “New -> Entity”.
为了简化开发过程，我们在CUBA Studio提供了可视化编辑器，使得JPA实体创建更容易。右键点击 “Data Model” ，然后选择 “New -> Entity” 。

First, we need to create the Speaker entity. It has three fields - first name which is mandatory, last name - optional, and email - mandatory and unique. Let’s add validation for the email field.
首先，我们需要创建 Speaker 实体，它有三个字段：first name（必须），last name（可选），以及 email（必须且唯一）。我们还要给 email 字段添加一个校验规则。

CUBA provides an ability to specify a proper entity display in the string format in the UI - “Instance Name”. For the speaker, we will select both the first and the last names.
CUBA 提供了一个功能，它可以将实体以字符串的形势展示在 UI 中 - “实体名称（Instance Name）”。对于 speaker，我们选择姓和名。

Let’s have a closer look at the entity designer. Next to the designer tab we can see generated text - JPA-annotated java class. They are integrated, so you can change the text and see all the changes in the designer and vice versa. Also, you can see the DDL Preview and can create indexes if required.
我们来详细了解下实体设计器。在Designer标签页旁边会看到生成的文本 - 一个使用了 JPA 注解的 Java 类。他们是集成的，所以你可以直接修改文本然后在Designer中看效果，反过来一样。你也能看到DDL预览，需要的话在其中创建索引。

For instance, we expect a lot of searches by last name. To make this search more efficient we can create an index for the last name field.
假如我们预见会有很多对 “姓” 的搜索，为了使搜索更有效率，我们可以对 last name字段添加索引。

Let’s move further and create the Session entity and link it to our Speaker class. First, define the attribute Topic - a mandatory string. After that, we will add the session start date and the time attribute. Add the duration of the session with validation. The session end date and time will be a calculated value, we’ll configure it a bit later. 
接下来，我们创建 Session 实体，并将它与 Speaker 类关联。首先，定义一个 Topic 属性 - 必须的字符串类型； 然后，添加 session start - 会议开始时间属性，然后是 duration - 带有校验规则的持续时长属性。session end - 会议结束时间是一个计算值，我们待会儿再配置它。

Now we’ll add a mandatory reference to the speaker. The relation is many-to-one, so, we’ll define an association field called speaker that references the speaker class. 
现在，添加一个必须的对 speaker 的引用。关系为多对一，我们把关联字段命名为 speaker，关联 Speaker 类。
 
In addition to this, we’ll create a description field that will contain a session’s long description. For the instance name we will select a topic field instead of the description.
此外，我们再创建一个描述字段（description）用来做会议的长文字描述。对于实体名称（Instance Name），我们选择topic字段，而不是description字段。

## Creating Calculated Attribute
## 创建计算属性

Now, we need to create an auto calculated attribute for the session’s end date and time. First add the getter method and name it getEndDate. Mark the method with the @MetaProperty annotation. For the calculated attribute, we need to specify the fields to be loaded. In our case, these are startDate and duration. Then just add calculation logic. 
现在我们为session的结束时间创建一个可以自动计算的属性。首先添加getter方法，命名为getEndDate，用 @MetaProperty 注解这个方法。对于计算属性，需要指定需要加载的属性。在我们这个例子中，需要加载的是开始时间和持续时间（startDate 和 duration），接下来就是添加计算逻辑。

Notice that Studio highlights the method because we need to specify a text label for the attribute. Let’s add it to the message bundle.
注意这里Studio会高亮这个方法，因为我们需要给属性指定文本标签，我们把它加到信息包中。

Now, go back to the designer and have a look. We see that the endDate field is added and it is read only and related attributes are specified.
现在回到设计器看一下。endDate字段已经加好了，并且是只读的，相关联的属性也指定了。

Our domain model has been created. Now, we can create a simple UI to perform CRUD operations over the tables in the database.
现在我们的领域模型已经创建好了。接下来，我们创建一个简单的界面对数据库中的表进行进行 CRUD 操作。

## Generating CRUD Screens
## 生成CRUD界面

CUBA Studio contains a UI screen generation wizard that helps us to create basic, but useful UI screens:
CUBA Studio 带有 UI 界面生成向导，可以帮我们创建基础却很有用的 UI 界面：

* Browser - to show the list of entities in a grid
* Editor - to edit one entity instance using a form-like user interface
* 浏览界面 - 在数据网格中展示实体列表
* 编辑界面 - 使用类似表单的界面编辑一个实体实例

First, we will create screens to work with speakers. Since this entity is pretty simple, we can accept the default parameters for the screen creation.
首先，我们创建有关 Speaker 的界面，由于实体结构非常简单，在创建界面时我们来直接使用默认参数。

As you can see, each screen consists of two parts: XML layout that defines the screen appearance with the preview feature, and a controller, written in java, which is responsible for internal screen logic and events handling.
可以看到，每个界面包含了两个部分：一个 XML 布局，定义了界面的展示形式并带有预览功能；还有一个控制器类，由 Java 编写，用来处理界面内部逻辑和事件处理。

Let’s take a look at the structure of the screen’s layout: there are two main blocks. The first one defines the way we are working with data and the second block defines the  layout  that specifies the positions of the components on the screen.
现在我们来看下界面布局的结构：分为两大块。第一块跟处理数据有关，第二块是布局，用来定义界面上组件的位置。

Now let’s create a browser and an editor for sessions. Here we need to stop for a while and explain some bits of CUBA.
现在我们为sessions创建一个浏览界面和一个编辑界面。开始之前， 需要先简单介绍一些CUBA相关的概念。

In the CUBA Platform, Entity View specifies which fields will be fetched from the database. You can define views in a separate file to use them in the different modules of your application, or create inline views while creating the screens.
在CUBA平台，实体视图（Entity View）指定了需要从数据库读取哪些字段。你可以在一个单独的文件里定义view，然后在不同的模块里使用；也可以在创建界面的时候创建内联的view。

So, let’s create an inline view. Just select the necessary data.
这次我们创建一个内联的view，只选择必要的数据（字段）。

You can see that the corresponding fields are added to the screens.
你会发现相关的字段已经加到界面了。

In addition, set the default duration value for the new session to one hour. To do this, go to the editor screen and subscribe to the event InitEntity and set this value in the code.
然后，为新session设置默认的持续时间为1小时。方法是：在编辑界面订阅 InitEntity 事件，然后通过代码设置。

Now, we need to generate scripts to create the database.
现在，我们需要生成创建数据的脚本。

## Database Creation
## 创建数据库

To generate scripts for the database creation, you need to select the CUBA - Generate Database Scripts menu.
要生成创建数据库的脚本，您需要使用 CUBA -> Generate DataBase Scripts 菜单。

To apply those scripts and create the database, just click Create database. Aside from application tables, CUBA creates system tables where we store information about users, roles, tasks, and others.
要应用这些脚本并创建数据库，需要使用 CUBA -> Create Database 菜单。除了我们为应用创建的表，CUBA 会创建一些系统表，用来存储用户、角色、任务等信息。

## Running the Application in Development Mode
## 运行开发模式的应用程序

Now we can run the application in development mode from the IDE.
现在我们从 IDE 启动开发模式的应用程序。

Just press “Run” in the IDE to start the application. You can see the application log in the studio’s “Run” window. After some time you can access the application using the browser. Let’s open the URL and log in to the application using  the username “admin”. The password is “admin” by default.
只要点击 IDE 的 “Run” 按钮，就可以启动应用程序了。您会在studio的 “Run” 窗口看到应用程序日志。等待一会您就可以使用浏览器访问应用了。我们打开这个 URL 并使用 “admin” 作为用户名登录系统，默认的密码是 “admin”。

Let’s add some test data to the application: first, let’s add a couple of speakers.
我们在应用程序中添加一些测试数据：首先，添加两个 speaker。

As you can see, email validation works as expected. 
可以看到，email的验证如期生效。

Now let’s add sessions for today and tomorrow. You can see that the endDate is calculated automatically.
现在为今天和明天各添加一个 Session。你会看到 endDate 被自动计算出来。

Generated screens are good for basic operations, but in the real world, the UI is usually more complex. Let’s add a calendar view to browse visits in addition to the grid view.
对于基本操作，生成的界面已经很好了。但是现实中的 UI 通常更加复杂，我们来在网格视图之外添加一个日历视图，用来浏览会议安排。

## Customizing the User Interface
## 定制用户界面

For the browser screen, we’ll add tab control, put a calendar on it and provide functionality to edit and reschedule sessions using this calendar.
对于浏览界面，我们会添加标签页，在标签页里加上日历，并提供通过日历组件编辑和重新安排会议的功能。

Let’s wrap the sessions table into tab sheet in the sessions browser screen. Add one more tab page and put a calendar control on it.
我们把 sessions 表格放到 session 浏览页面的标签页上，再添加另一个标签页，放一个日历控件在上面。

Expand the TabSheet element to the whole screen. Studio asks for an id. In CUBA, we need IDs to reference a screen element in the code.
用TabSheet撑满界面，Studio会要求一个id。在CUBA中，代码通过ID标识一个界面元素。

Also, let’s set id-s and captions for each tab.
我们也需要为每个tab设置id和标题。

Update the calendar - assign a data container and expand it.
修改日历 - 为他分配数据容器然后使它撑满界面。

Finally, expand the Session Table.
最后，使Session表格也撑满界面。

In CUBA, UI components are bound to entities and their properties.
在 CUBA 中，UI 组件一般要绑定到实体及其属性上。

We will bind the calendar to the data collection fetched in the screen. Use the search field to find properties and bind:
我们将日历组件绑定到界面中获取的数据集。使用搜索域找到属性然后绑定：

* startDateProperty to a session’s start Date
* endDateProperty to a session’s end Date
* captionProperty to a session’s topic
* And descriptionProperty to a session’s description
* startDateProperty 绑定到 session 的开始时间
* endDateProperty 绑定到 session 的结束时间
* captionProperty 绑定到 session 的主题
* descriptionProperty 绑定到 session 的描述

Let’s make the calendar to show only working hours.
我们让日历只显示工作时间。

In addition to the visual designer, you can use the XML markup editor. Let’s add navigation buttons.
除了可视化编辑器，你也可以使用xml编辑器。我们来添加导航按钮。

We can reopen the form to see the changes - the CUBA framework supports hot reload for screens. Now we can see sessions displayed on the calendar.
我们可以重新打开表单来查看变化 - CUBA框架支持界面热部署。现在我们会看到 session 都在日历上显示了。

## Using Screen API
## 使用界面API

Now let’s invoke the session editor screen when we click on an event in the calendar. 
现在我们来实现在日历中点击一个事件时，调用 session 的编辑界面。

When we interact with the UI, events are generated. We can subscribe to those events to handle them. Let’s handle a click on a calendar’s entry. 
当我们与 UI 交互时，会产生交互事件。我们可以订阅这些事件并进行处理。我们来处理一下日历条目的点击事件。

We need to invoke the editor screen to change the session’s properties. Let’s use the EditorScreenFacet. It is a non-visual component that provides an ability to pre-configure an editor screen.
我们需要使用编辑界面来修改session的属性。我们来用EditorScreenFacet， 这是一个非可视化组件，可以预先配置编辑界面。

Put it under the window element in the Component Hierarchy window. And set the properties:
把它放在组件树窗口的window元素下，设置以下属性：

* an ID
* We need an editor for the session class. Set the Entity class - session and the corresponding data container. 
* Screen class - SessionEdit.
* Set the edit mode.
* The screen will be opened in the dialog mode.
* ID
* 我们在为session类创建编辑界面。所以设置实体类 - session 还有相关的数据容器。
* 界面类 - SessionEdit
* 设置编辑模式（edit mode）
* 界面会以dialog模式打开


Go back to the event handler.  Inject the EditorScreenFacet. Pass the session entity received within the event object for editing. After that, we should show the editor.
回到事件处理器。Inject EditorScreenFacet，传入事件中指定的被编辑的session实体。然后我们就可以展示编辑界面了。

Let’s reopen the screen and invoke the editor.
我们来重新打开界面，调用编辑界面。

As you can see, we need to refine the session editor dialog to make it look better by adjusting its width and height. The simplest way to do this is set “auto” for both width and height.
如您所见，为了使 session 编辑对话框更好看些，我们需要对宽度和高度进行微调。最简单的方法是把宽度和高度都设置为“auto”。

Let’s reopen the screen again and see the changes.
我们重新打开界面看看改动。

Now it is time to add some business logic to our application.
现在该为我们的应用程序添加一些业务逻辑了。

## Adding Business Logic
## 添加业务逻辑

In this section, we will use CUBA Studio to create a service that implements business logic and use this service in a screen controller. It will be a service for session rescheduling that will ensure that one speaker doesn’t have more than two sessions in one day.
这一节我们将用 CUBA Studio 创建一个服务用来实现业务逻辑，然后在界面控制器中使用它。这个服务将用来重新安排会议，并保证一个 speaker 不会在同一天内有两个以上的会议。

Right-click on the service node in the CUBA project tree and create a SessionService interface and SessionServiceBean implementation. Create a method in the interface and implement it in the ServiceBean.
右键点击 CUBA 项目树的 service 节点，创建 SessionService 接口和 SessionServiceBean 实现。在接口内创建一个方法然后在 ServiceBean 中实现它。

The method will accept a session and a new session date and time. The service will return the updated Session instance.
该方法接受一个 session 参数和一个新的 session 时间，然后返回修改的 Session 实例。

First, calculate the start and end time of the day where the session is planned.
首先，计算会议的开始和结束时间。

We will use CUBA API for data access - the DataManager class.
我们将会使用 CUBA 的 API 来访问数据 - DataManager 类。

With DataManager we create a JPQL query to check if there are any sessions scheduled for the speaker in a defined time span and add parameter values to it. 
使用 DataManager 我们创建一个 JPQL 查询语句来检查在指定的时间段内是否有为 Speaker 安排的会议，并且添加参数值。

Then we check the query result and, depending on the result, we update the session with a new start date or just return the original session instance.
然后我们检查查询结果，依据查询结果，我们为会议设置新的开始日期或者直接返回原session实例。

## Using Service in Screens
## 在界面中使用Service(服务)

The service is ready, now let’s add it to the session browser screen. It will be invoked for the drag-and-drop event in the calendar. 
service已经写好了，现在我们把它加到session浏览界面。这个服务会在日期的拖拽事件中调用。

In the method that is subscribed to the drag-and-drop event, we add some code to extract the session entity from the calendar event and pass it to the service to reschedule this session. After that, we just update this item in the browser’s data container.
在订阅拖拽事件的方法中，我们添加一些代码用来从日历事件中抽取 session 实体，然后调用服务来重新计划 session。然后，我们直接在浏览界面的data container中更新对应的session。

To have the new service redeployed, we need to restart the application. 
为了部署新的service，我们需要重启应用程序。

After restarting we can open the sessions calendar - and voila! We have drag-and-drop rescheduling functionality for the calendar! Let’s see how it works. Just add a couple of extra sessions and try to reschedule one of the sessions to the day that is already full. 
重启之后，打开会议日历，搞定！我们的日历有通过拖拽重新安排会议的功能了！我们来看看能否正常运行，再添加两个额外的会议，然后尝试把其中一个重新计划到日程已满的一天。

## Branding

## 品牌

In this section we will customize standard captions in the application, adding some branding. 
在这节中，我们将会对应用程序的默认标题做自定义修改，添加一些品牌信息。

In CUBA, you can update resource files and override the standard text. Let’s update the text according to the application business domain - conference planning.
在 CUBA 中，您可以修改资源文件并且重写默认的标准文字。根据应用程序的业务领域我们修改标题为 - 大会计划。

With CUBA’s hot deploy feature, all we need to do is to log in to the application again to see the changes.
得益于 CUBA 的热部署功能，我们重新登录应用程序即可看到变化。

## Marketplace
## 应用市场

The framework comes with a marketplace that contains plenty of components, so you can easily add useful features like charts or maps support to the existing application.
CUBA 框架还有一个应用市场，里面有很多组件，这样您可以非常方便的添加有用的功能，比如为已有的应用程序添加图表或地图支持。

You can install those components right from the studio using the “Marketplace” menu. 
可以通过 Studio 的 “Marketplace” 菜单直接安装这些组件。

Let’s add the Helium add-on. It’s a new visual theme that you can use instead of the standard themes. Just click install and apply the changes.
我们来添加Helium扩展。这是一个新的样式主题，你可以用它替换标准主题，点击安装然后应用就可以了。

Now we need to stop the application and apply the init scripts of the add-on.
现在我们停止程序，运行组件的init脚本。

Run the application and go to the settings screen. You can find the added theme in the drop-down list. Select it and apply. Log in to the application again - theme is already applied. We can also open the theme settings screen and change settings with preview.
运行程序，打开设置界面。你能在下拉框看到新添加的主题，选择它然后应用。重新登录系统 - 主题就会生效了。你也可以打开主题设置界面，修改它，并且预览你的改动。

## Conclusion
## 总结

The CUBA framework provides a lot of useful APIs to help you create business applications quickly. This quickstart shows only the basics of the CUBA framework and CUBA Studio. 
CUBA 框架提供很多有用的 API，能帮您快速的创建业务系统。这个快速开始视频只演示了最基本的 CUBA 框架和 CUBA Studio 功能。

You can find more examples and tutorials on our website: cuba-platform.com.
访问我们的网站 cuba-platform.cn 您能找到更多的示例和向导。

Thank you for watching!
感谢观看！

# Application. Captions
# 应用.标题

Quick start
快速开始

A full stack Java framework for business applications development
用于业务应用程序开发的全栈 Java 框架

Powered by
动力源自

Data model and database creation
数据模型和数据库创建

User interface development
用户界面开发

Business logic implementation
业务逻辑实现

Thank you for watching
感谢观看